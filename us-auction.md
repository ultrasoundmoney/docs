# US auction

The ultra sound relay now has a second instance in the US complementing the EU instance. Proposer requests are automatically routed to the geographically closest instance.

## Bid submission
Builders can only submit bids to the US relay instance using a direct auction connection—see instructions [here](https://github.com/ultrasoundmoney/docs/blob/main/direct-auction-connections.md). 

## Bid sharing
Bids are not shared across relay instances. A block submitted to the EU instance will not be reflected in the US instance and vice versa. Builders are expected to submit bids to both instances.

## Colocation
The US instance runs on OVH bare metal machines in the VIN (Vint Hill) data center, [roughly 2.5ms from AWS us-east-1](https://was1-vin.smokeping.ovh.net/smokeping??&target=USA.AS16509-us-east-1). Colocation requires an account on [OVH US]([us.ovhcloud.com](https://us.ovhcloud.com/)).

## Routes
Direct routes are supported in eu and us. Following the guide under "Connecting" above to receive the private address. Routes are:
- `/relay/v1/builder/blocks`
- `/relay/v1/builder/validators`
- `/eth/v1/builder/header`
- `/ws/v1`

Some builder routes e.g. blocks received (`/relay/v1/data/bidtraces/builder_blocks_received`) will only return results related to the geography found in the subdomain e.g. Subdomains are:
- https://relay-builders-us.ultrasound.money
- https://relay-builders-eu.ultrasound.money

Further documentation on which resources exactly are served through the builder URL pending...

If you need the data to build blocks, or do analytics on what has been built _as a builder_ the above should suffice. If you're not a builder and ended up here, or for some reason really want a global view, there is https://relay-analytics.ultrasound.money .
