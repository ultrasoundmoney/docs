# New Builder Guide

## First steps
1) colocation: Locate your builder close to relays. The ultra sound relay is at OVH Roubaix (RBX), France.
2) submission format: Submit your bids using SSZ (not JSON) and GZIP compression.
3) submission frequency: The ultra sound relay does not have rate limitting which means you can increase the frequency at which you send blocks to us. Top builders make a few thousand submissions per block landed.
4) optimistic relaying: This is a massive relay-side optimisation where bids are processed optimistically, skipping simulation and saving on simulation latency. Almost all builders (22 of them!) have enabled optimistic relaying with the ultra sound relay—it's hard to compete without. Here is an onboarding guide.
5) top bid websocket: To make competitive bids a builder needs to be aware of the auction's bid-to-beat, the top bid. We offer a websocket to efficiently keep track. Read more here: https://github.com/ultrasoundmoney/docs/blob/main/top-bid-websocket.md
6) use http/2 or `Connection: keep-alive` if using http/1

## URL
For staging / holesky you may submit to: `relay-builders-eu-holesky.ultrasound.money`.

For production / mainnet you may submit to: `relay-builders-eu.ultrasound.money`.

## Optimistic Relaying
See: [optimistic-relaying-builder-guide.md](https://github.com/ultrasoundmoney/docs/blob/main/optimistic-relaying-builder-guide.md)

## Bid Adjustments
See: [bidadjustment.md](https://github.com/ultrasoundmoney/docs/blob/main/bid_adjustment.md)

### Data API
Moved to: [bidadjustment.md#data-api](https://github.com/ultrasoundmoney/docs/blob/main/bid_adjustment.md#data-api)
