# US Auction

We've started experimimenting with running a US relay instance. On a high level, our relay tries to identify which relay instance would have the lowest latency connection to a proposer and route proposer requests for headers there. In simple terms, a proposer next to the US instance would not see bids from the EU instance.

## Connecting
Currently we only accept submissions from "directly connected builders" to our US relay. To become one follow the steps in the [direct-auctions-connection guide](https://github.com/ultrasoundmoney/docs/blob/main/direct-auction-connections.md).

## Co-locating
Our instances run on OVH bare metal machines in their VIN (Vint Hill) data center. You can find their services on offer over on us.ovhcloud.com . OVH also maintains a dashboard showing the latency to many other data centers. The closest popular one being [AWS us-east-1](https://was1-vin.smokeping.ovh.net/smokeping??&target=USA.AS16509-us-east-1&) in Virginia, 2.5ms away.

## Bid sharing
Currently bids are not shared between instances. Meaning, a block submitted through our EU builder routes will not show up in the US, even if the bid is higher. This means we rely on builders to submit competitive bids to both relays at all times.

## Routes
Direct routes are supported in eu and us. Following the guide under "Connecting" above to receive the private address. Routes are:
- /relay/v1/builder/blocks
- /relay/v1/builder/validators
- /eth/v1/builder/header
- /ws/v1

Some builder routes e.g. blocks received (`/relay/v1/data/bidtraces/builder_blocks_received`) will only return results related to the geography found in the subdomain e.g. Subdomains are:
- https://relay-builders-us.ultrasound.money
- https://relay-builders-eu.ultrasound.money

Further documentation on which resources exactly are served through the builder URL pending...

If you need the data to build blocks, or do analytics on what has been built _as a builder_ the above should suffice. If you're not a builder and ended up here, or for some reason really want a global view, there is https://relay-analytics.ultrasound.money .
