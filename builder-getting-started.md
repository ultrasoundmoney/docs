# New Builder Guide

## First steps
1) colocation: Locate your builder close to relays. The ultra sound relay is at OVH Roubaix (RBX), France.
2) submission format: Submit your bids using SSZ (not JSON) and GZIP compression.
3) submission frequency: The ultra sound relay does not have rate limitting which means you can increase the frequency at which you send blocks to us. Top builders make a few thousand submissions per block landed.
4) optimistic relaying: This is a massive relay-side optimisation where bids are processed optimistically, skipping simulation and saving on simulation latency. Almost all builders (22 of them!) have enabled optimistic relaying with the ultra sound relay—it's hard to compete without. You can find a link to the on-boarding guide below.
5) top bid websocket: To make competitive bids a builder needs to be aware of the auction's bid-to-beat, the top bid. We offer a websocket to efficiently keep track. Read more here: https://github.com/ultrasoundmoney/docs/blob/main/top-bid-websocket.md
6) use http/2 or `Connection: keep-alive` if using http/1

## URL
For staging / holesky use: `https://relay-builders-eu-holesky.ultrasound.money` or `https://relay-builders-us-holesky.ultrasound.money`.

For production / mainnet use: `https://relay-builders-eu.ultrasound.money` or `https://relay-builders-us.ultrasound.money`.

## Transaction Filtering

The ultra sound relay supports both transaction filtering and non-filtering proposers. A proposer's filtering preference is communicated in the `/relay/v1/builder/validators` response. Possible values are `none` for no filtering, and `ofac` for filtering according to [this list](https://github.com/ultrasoundmoney/ofac-ethereum-addresses/blob/main/data.csv). By default, the endpoint only returns proposers with filtering set to `none`. To receive both filtering and non-filtering proposers in the lookahead, add the following query parameter: `?filtering=true`. A block containing filtered addresses in a filtered slot leads to a simulation failure. Below is an example response.

```json
 {
    "slot": "11411773",
    "validator_index": "1427827",
    "entry": {
      "message": {
        "fee_recipient": "0x4675c7e5baafbffbca748158becba61ef3b0a263",
        "gas_limit": "36000000",
        "timestamp": "1734571166",
        "pubkey": "0xb6e245c28445330cb858d52ad37190b41f8933bafa15a426d12864fac8af4b209920bc44374f15b2ccbab9fcd70cc9bb"
      },
      "signature": "0x8dc6efff70584da37d256459c39e457681574d615263c248af9c7cc46cfef104994762b9fc7d3a2ddf594359d2a258170e507fe09c9984405e11559b1b35ea6da4dc5a8b1938a6afd46ced846d0d4ffd3a8643b1ec794e371951aa790a07f4c8"
    },
    "preferences": {
      "filtering": "none"
    }
  },
```

## Optimistic Relaying
See: [optimistic-relaying-builder-guide.md](https://github.com/ultrasoundmoney/docs/blob/main/optimistic-relaying-builder-guide.md)

## Bid Adjustments
See: [bidadjustment.md](https://github.com/ultrasoundmoney/docs/blob/main/bid_adjustment.md)

### Data API
Moved to: [bidadjustment.md#data-api](https://github.com/ultrasoundmoney/docs/blob/main/bid_adjustment.md#data-api)
