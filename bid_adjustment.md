# Bid adjustment

Bid adjustment is a new experimental feature for the ultra sound relay. The idea is that we try to adjust bids, ideally to `secondBestBid + 1 WEI`, capturing the delta (if any) from our latency advantage compared the the next best relay. Our goal is to make operating and developing (e.g. geo distribution, proper cancellations) the relay sustainable by having an incentive tied to its performance.

To achieve this, we need block submissions to include some additional data. As an incentive for builders to integrate we offer a percentage of the bid delta as a "kickback". During November 100% of the delta will be kicked back to builders.

## Technical implementation

Enabled by `?adjustments=1` and by including an `adjustment_data` object in the normal block submission:

```json
{
 "message": { ... },
 "execution_payload": { ... },
 "signature": "0xa2def54237bfeb1d9269365e853b5469f68b7f4ad51ca7877e406ca94bc8a94bba54c14024b2f9ed37d8690bb9fac52600b7ff52b96b843cd8529e9ecc2497a0ecd5db8372e2049156e0fa9334d5c1b0ef642f192675b586ecbe6fc381178f88",
 "adjustment_data": {
    "state_root": "0x74f74d15dcb00ba194901136f2019dd6be2d4c88c822786df90561a550193899",
    "transactions_root": "0x74f74d15dcb00ba194901136f2019dd6be2d4c88c822786df90561a550193899",
    "receipts_root": "0x74f74d15dcb00ba194901136f2019dd6be2d4c88c822786df90561a550193899",
    "builder_address": "0xb64a30399f7f6b0c154c2e7af0a3ec7b0a5b131a",
    "builder_proof": ["0xabc", "0x123", ...]
    "fee_recipient_address": "0xb64a30399f7f6b0c154c2e7af0a3ec7b0a5b131a",
    "fee_recipient_proof": ["0xabc", "0x123", ...],
    "fee_payer_address": "0xb64a30399f7f6b0c154c2e7af0a3ec7b0a5b131a",
    "fee_payer_proof": ["0xabc", "0x123", ...],
    "placeholder_transaction_proof": ["0xabc", "0x123", ...],
    "placeholder_receipt_proof": ["0xabc", "0x123", ...]
 }
}
```

- `builder_address` is the usual builder address that pays the proposer in the last transaction of the block. When we adjust a bid, this transaction is overwritten by a transaction from the collateral account `fee_payer_address`. If we don't adjust the bid, `builder_address` pays the proposer as per usual.
- `fee_payer_address` is an account which holds the ETH used by the relay to pay the fee recipient. Builders fund this account to use the feature. All adjusted bids are paid from this address.
- `fee_recipient_address` is the proposer's fee recipient.
- `placeholder_transaction_proof` is the merkle proof for the last transaction in the block, which will be overwritten with a payment from `fee_payer` to `fee_recipient` if we adjust the bid.
- `placeholder_receipt_proof` is the merkle proof for the receipt of the placeholder transaction. It's required for adjusting payments to contract addresses.

Note that we rely on the gas_limit of the payout transaction being exact, i.e. 21000 for EOA recipient and variable for contract recipients.

### Computing the adjustment data

Example using `flashbots/builder` and `go-ethereum` libraries:

```go
type AdjustmentData struct {
	BuilderAddress      *common.Address  `json:"builder_address"`
	BuilderProof        *[]hexutil.Bytes `json:"builder_proof"`
	FeeRecipientAddress *common.Address  `json:"fee_recipient_address"`
	FeeRecipientProof   *[]hexutil.Bytes `json:"fee_recipient_proof"`
	FeePayerAddress     *common.Address  `json:"fee_payer_address"`
	FeePayerProof       *[]hexutil.Bytes `json:"fee_payer_proof"`
	PlaceholderTxProof  *[]hexutil.Bytes `json:"placeholder_transaction_proof"`
	StateRoot           *common.Hash     `json:"state_root"`
	TransactionsRoot    *common.Hash     `json:"transactions_root"`
    ReceiptsRoot        *common.Hash     `json:"receipts_root"`
}

func (w *worker) computeAdjustmentData(env *environment, validatorCoinbase *common.Address, feePayerAddr *common.Address) (*types.AdjustmentData, error) {
	// Account proofs
	builderProof, _ := env.state.GetProof(w.coinbase)
	hexBuilderProof := make([]hexutil.Bytes, len(builderProof))
	for i, v := range builderProof {
		hexBuilderProof[i] = hexutil.Bytes(v)
	}

	feeRecipientProof, _ := env.state.GetProof(*validatorCoinbase)
	hexFeeRecipientProof := make([]hexutil.Bytes, len(feeRecipientProof))
	for i, v := range feeRecipientProof {
		hexFeeRecipientProof[i] = hexutil.Bytes(v)
	}

	feePayerProof, _ := env.state.GetProof(*feePayerAddr)
	hexFeePayerProof := make([]hexutil.Bytes, len(feePayerProof))
	for i, v := range feePayerProof {
		hexFeePayerProof[i] = hexutil.Bytes(v)
	}

	// Placeholder tx proof
	placeholderTransactionIndex := len(env.txs) - 1
	var transactions types.Transactions = env.txs
	transactionTrie := populateTrie(transactions)
	transactionProofDb := rawdb.NewMemoryDatabase()
	transactionKey, _ := rlp.EncodeToBytes(uint(placeholderTransactionIndex))
	transactionTrie.Prove(transactionKey, 0, transactionProofDb)
	transactionIter := transactionProofDb.NewIterator(nil, nil)
	var placeholderTransactionProof []hexutil.Bytes
	for transactionIter.Next() {
		placeholderTransactionProof = append(placeholderTransactionProof, transactionIter.Value())
	}
	transactionIter.Release()

	// Placeholder tx receipt proof
	receiptKey, _ := rlp.EncodeToBytes(uint64(placeholderTransactionIndex))
	var receipts types.Receipts = env.receipts
	receiptTrie := populateTrie(receipts)
	receiptProofDb := rawdb.NewMemoryDatabase()
	receiptTrie.Prove(receiptKey, 0, receiptProofDb)
	receiptIter := receiptProofDb.NewIterator(nil, nil)
	var placeholderReceiptProof []hexutil.Bytes
	for receiptIter.Next() {
		placeholderReceiptProof = append(placeholderReceiptProof, receiptIter.Value())
	}
	receiptIter.Release()

	transactionsRoot := types.DeriveSha(transactions, trie.NewStackTrie(nil))
	receiptsRoot := types.DeriveSha(receipts, trie.NewStackTrie(nil))

	return &types.AdjustmentData{
		BuilderAddress:          &w.coinbase,
		BuilderProof:            &hexBuilderProof,
		FeeRecipientAddress:     validatorCoinbase,
		FeeRecipientProof:       &hexFeeRecipientProof,
		FeePayerAddress:         feePayerAddr,
		FeePayerProof:           &hexFeePayerProof,
		PlaceholderTxProof:      &placeholderTransactionProof,
		PlaceholderReceiptProof: &placeholderReceiptProof,
		StateRoot:               &env.header.Root,
		TransactionsRoot:        &transactionsRoot,
		ReceiptsRoot:            &receiptsRoot,
	}, nil

}

// Helpers

// encodeBufferPool holds temporary encoder buffers for DeriveSha and TX encoding.
var encodeBufferPool = sync.Pool{
	New: func() interface{} { return new(bytes.Buffer) },
}

func encodeForDerive(list types.DerivableList, i int, buf *bytes.Buffer) []byte {
	buf.Reset()
	list.EncodeIndex(i, buf)
	// It's really unfortunate that we need to do perform this copy.
	// StackTrie holds onto the values until Hash is called, so the values
	// written to it must not alias.
	return common.CopyBytes(buf.Bytes())
}

// Adapted from core/types/hashing.go
func populateTrie(txs types.DerivableList) *trie.Trie {
	db := rawdb.NewMemoryDatabase()
	triedb := trie.NewDatabase(db)
	t := trie.NewEmpty(triedb)

	valueBuf := encodeBufferPool.Get().(*bytes.Buffer)
	defer encodeBufferPool.Put(valueBuf)

	var indexBuf []byte
	for i := 1; i < txs.Len() && i <= 0x7f; i++ {
		indexBuf = rlp.AppendUint64(indexBuf[:0], uint64(i))
		value := encodeForDerive(txs, i, valueBuf)
		t.Update(indexBuf, value)
	}
	if txs.Len() > 0 {
		indexBuf = rlp.AppendUint64(indexBuf[:0], 0)
		value := encodeForDerive(txs, 0, valueBuf)
		t.Update(indexBuf, value)
	}
	for i := 0x80; i < txs.Len(); i++ {
		indexBuf = rlp.AppendUint64(indexBuf[:0], uint64(i))
		value := encodeForDerive(txs, i, valueBuf)
		t.Update(indexBuf, value)
	}
	return t

}
```

### SSZ encoding

We strongly suggest SSZ (+ gzip) encoding all submissions for the best performance. The `AdjustmentData` is expected as the last field in `SubmitBlockRequest` (see JSON example above). Here's how to encode `AdjustmentData` as SSZ:

```
state_root: FixedVector<u8; 32>
transactions_root: FixedVector<u8; 32>
receipts_root: FixedVector<u8; 32>
builder_address: FixedVector<u8; 20>
builder_proof: VariableList<VariableList<u8>>
fee_recipient_address: FixedVector<u8; 20>
fee_recipient_proof: VariableList<VariableList<u8>>
fee_payer_address: FixedVector<u8; 20>
fee_payer_proof: VariableList<VariableList<u8>>
placeholder_transaction_proof: VariableList<VariableList<u8>>
placeholder_receipt_proof: VariableList<VariableList<u8>>
```

An example of the SSZ codec in go: https://github.com/blombern/adjustable-bid-encoding

## Testing

We've successfully tested this feature using our own builder on Goerli, and we're now looking to integrate with external builders. Testing can be done on either Goerli or Mainnet (with optimistic relaying disabled).

## Performance considerations

The additional size to an uncompressed json payload (worst case) is 22KB. We recommend SSZ encoding payloads for significantly faster decoding.

On the relay side, the adjustment computation cost is negligible at ~300μs
