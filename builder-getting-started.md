# New Builder Guide

## First steps
1) colocation: Locate your builder close to relays. The ultra sound relay is at OVH Roubaix (RBX), France.
2) submission format: Submit your bids using SSZ (not JSON) and GZIP compression.
3) submission frequency: The ultra sound relay does not have rate limitting which means you can increase the frequency at which you send blocks to us. Top builders make a few thousand submissions per block landed.
4) optimistic relaying: This is a massive relay-side optimisation where bids are processed optimistically, skipping simulation and saving on simulation latency. Almost all builders (22 of them!) have enabled optimistic relaying with the ultra sound relay—it's hard to compete without. Here is an onboarding guide.
5) top bid websocket: To make competitive bids a builder needs to be aware of the auction's bid-to-beat, the top bid. We offer a websocket to efficiently keep track. Read more here: https://github.com/ultrasoundmoney/docs/blob/main/top-bid-websocket.md

## Optimistic Relaying
See: [builder-onboarding.md](https://github.com/ultrasoundmoney/mev-boost-relay/blob/prod-optimistic-relaying/docs/optimistic/builder-onboarding.md)

## Bid Adjustments
See: [bidadjustment.md](https://gist.github.com/blombern/c2550a5245d8c2996b688d2db5fd160b)

### Data API
To see what adjustments were made, we offer an API.

https://relay.ultrasound.money/ultrasound/v1/data/adjustments?slot=7964405

The API expects a single query parameter, slot, indicating the slot you'd like to see the adjustments for. The earliest slot available in our production environment is `7869470`.

For a successful call the API returns a body with a field `data` with zero or more adjustments. Amounts are in Wei.
Example:
```json
{
  "data": [
    {
      "adjusted_block_hash": "0x396fc66c421240af8a0b95598e7a2d1fc0e6ceafd18e4d128b96f918de7928e8",
      "adjusted_value": "137531619981380959",
      "block_number": 18771162,
      "builder_pubkey": "0xa32aadb23e45595fe4981114a8230128443fd5407d557dc0c158ab93bc2b88939b5a87a84b6863b0d04a4b5a2447f847",
      "delta": "26254846002041",
      "submitted_block_hash": "0xfd8c11c274dc1d631a1a5233f5f79e33cbc9f7abbb114ba630f21de5b8fb10c2",
      "submitted_received_at": "2023-12-12T16:01:23.154418Z",
      "submitted_value": "137557874827383000"
    },
    {
      "adjusted_block_hash": "0xd0ed51aa188e24836c03dd9c9b1a5c2bb96e29d32ab7b92055f073b8e423ef8b",
      "adjusted_value": "137558036898280751",
      "block_number": 18771162,
      "builder_pubkey": "0xa32aadb23e45595fe4981114a8230128443fd5407d557dc0c158ab93bc2b88939b5a87a84b6863b0d04a4b5a2447f847",
      "delta": "267665489777549",
      "submitted_block_hash": "0x9be20ae9fe1e35638050b22da5eaf17042e58065802e12774cebe89b2779f3c4",
      "submitted_received_at": "2023-12-12T16:01:23.245729Z",
      "submitted_value": "137825702388058300"
    }
  ]
}
```

In the event adjustments are requested for a slot which started less than 12 seconds ago, a 403 + JSON body with a single field `error` is returned, explaining the relay refuses to share data for slots that young.

Most bad requests will similarly have a JSON body with an `error` field. In rare error cases you may see no body, or a string body explaining the problem.
