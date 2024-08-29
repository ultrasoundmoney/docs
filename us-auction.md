# US auction

The ultra sound relay now has a second instance in the US complementing the EU instance. Proposer requests are automatically routed to the geographically closest instance.

## Bid submission
Builders can only submit bids to the US relay instance using a direct auction connection—see instructions [here](https://github.com/ultrasoundmoney/docs/blob/main/direct-auction-connections.md). 

## Bid sharing
Bids are not shared across relay instances. A bid submitted to the EU instance will not be reflected in the US instance and vice versa. Builders are expected to submit bids to both instances.

## Colocation
The US instance runs on OVH bare metal machines in the VIN (Vint Hill) data center, [roughly 2.5ms from AWS us-east-1](https://was1-vin.smokeping.ovh.net/smokeping??&target=USA.AS16509-us-east-1). Colocation requires an account on [OVH US]([us.ovhcloud.com](https://us.ovhcloud.com/)).

## Subdomains
Each local instance has a dedicated subdomain:

- https://relay-builders-us.ultrasound.money
- https://relay-builders-eu.ultrasound.money

For a global data view across relay instances use the analytics subdomain:

- https://relay-analytics.ultrasound.money

## Routes
The following direct routes are available for the EU and US instances:
- `/relay/v1/builder/blocks`
- `/relay/v1/builder/validators`
- `/eth/v1/builder/header`
- `/ws/v1/top_bid`

Some builder routes (e.g. `/relay/v1/data/bidtraces/builder_blocks_received`) will only return results for the corresponding instances.

(Further documentation on which resources are served through the builder URL pending.)
