# US Auction

We've started experimimenting with running a US relay instance.

## Connecting
Currently we only accept submissions from "directly connected builders" to our US relay. To become one follow the steps in the [direct-auctions-connection guide](https://github.com/ultrasoundmoney/docs/blob/main/direct-auction-connections.md).

On a high level, our relay tries to identify which relay instance would have the lowest latency connection to a proposer and route proposer requests for headers there. In simple terms, a proposer next to the US instance would not see bids from the EU instance.

## Co-locating
Our instances run on OVH bare metal machines in their VIN (Vint Hill) data center. You can find their services on offer over on us.ovhcloud.com . OVH also maintains a dashboard showing the latency to many other data centers. The closest popular one being [AWS us-east-1](https://was1-vin.smokeping.ovh.net/smokeping??&target=USA.AS16509-us-east-1&) in Virginia, 2.5ms away.

## Bid sharing
Currently bids are not shared between instances. Meaning, a block submitted through our EU builder routes will not show up in the US, even if the bid is higher. This means we rely on builders to submit competitive bids to both relays at all times.

## Data APIs
Builder subdomains will only return local data e.g. https://relay-builders-us.ultrasound.money/relay/v1/data will only share submissions and adjustments from the US auction. You need https://relay-builders-eu... to see their equivalents in the EU. If you're not a builder, and would rather query a global view, you may do so through https://relay-analytics.ultrasound.money .
