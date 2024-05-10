Endpoint: ws://relay-builders-eu.ultrasound.money/ws/v1/top_bid

It sends ping frames, clients should respond with pong.

It emits SSZ encoded data of the following (rust) types:
```rust
pub struct TopBidUpdate {
    pub timestamp: u64,
    pub slot: u64,
    pub block_number: u64,
    pub block_hash: B256,
    pub parent_hash: B256,
    pub builder_pubkey: BlsPublicKey,
    pub fee_recipient: Address,
    pub value: U256,
}
```

Use persistent connections if possible. When closing connections please make sure to close the socket properly.
