# optimistic v3

NOTE: this API is still being tested and is subject to change.

This document describes the ultra sound relay V3 submission endpoint, which includes adjustments. For a description of vanilla V3 submissions, see [here](https://ethresear.ch/t/introduction-to-optimistic-v3-relays/22066). Like vanilla V3 submissions, these are only available for optimistic/collateralized builders. Unlike for our V1 submissions, submission adjustability is not optional. This is done to cover the extra operational expense of a builder dependency in the critical path, and to enable value-only bid updates in the future.

Submissions are otherwise identical to vanilla V3, except they include adjustment data:

```rust
// Electra submission
pub struct HeaderSubmission {
    pub url: Vec<u8>,
    pub tx_count: u32,
    pub submission: SignedAdjustableHeaderSubmission,
}

pub struct SignedAdjustableHeaderSubmission {
    pub message: AdjustableHeaderSubmission,
    pub signature: BlsSignature,
}

pub struct AdjustableHeaderSubmission {
    pub bid_trace: BidTrace,
    pub execution_payload_header: ExecutionPayloadHeader,
    pub execution_requests: ExecutionRequests,
    pub commitments: Vec<Bytes48>,
    // Additional field
    pub adjustment_data: AdjustmentDataV2,
}

// EL: execution layer, CL: consensus layer
pub struct AdjustmentDataV2 {
    pub el_transactions_root: B256,
    pub el_withdrawals_root: B256,
    pub builder_address: Address,
    pub builder_proof: Vec<Bytes>,
    pub fee_recipient_address: Address,
    pub fee_recipient_proof: Vec<Bytes>,
    pub fee_payer_address: Address,
    pub fee_payer_proof: Vec<Bytes>,
    pub el_placeholder_transaction_proof: Vec<Bytes>,
    // New in V2: SSZ merkle proof for last transaction
    pub cl_placeholder_transaction_proof: Vec<B256>,
    pub placeholder_receipt_proof: Vec<Bytes>,
    // New in V2: Logs bloom accrued until but not including the last (payment) transaction.
    pub pre_payment_logs_bloom: Bloom,
}
```

~~A working POC builder implementation can be found~~ [~~here~~](https://github.com/blombern/rbuilder/tree/optimistic-v3-adjustments)~~.~~

A production implementation can be found in the rbuilder repo: [https://github.com/flashbots/rbuilder](https://github.com/flashbots/rbuilder)

The endpoint is `/relay/v3/builder/headers` and it's available on our Hoodi relay: `relay-builders-eu-hoodi.ultrasound.money`/`relay-builders-us-hoodi.ultrasound.money`.

## Error responses

If the payload retrieval from the builder supplied URL fails, the builder is demoted. V3 submissions from pessimistic builders will be rejected and receive the following HTTP 400 response:

```json
{
  "code": 400,
  "message": "received v3 submission from pessimistic builder"
}
```

This can be used as a trigger to fall back to V1 submissions until re-promotion.

In some cases, we may be temporarily unable to verify a builder's optimistic status, i.e. we don't know if the builder is optimistic or not. This could happen from time to time, especially early in the bidding window. We distinguish this case from a true pessimistic status by returning the following HTTP 503 response:

```json
{
  "code": 503,
  "message": "temporarily unable to verify builder optimistic collateral"
}
```

Another rejection case is if the value of the submission exceeds builder collateral. In these cases the response will be:

```json
{
  "code": 400,
  "message": "received v3 submission with value above optimistic collateral"
}
```
