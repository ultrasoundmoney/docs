# US Auction

We've started experimimenting with running a US relay. Currently we only accept submissions from "directly connected builders" there. To become one follow the steps in the [direct-auctions-connection guide](https://github.com/ultrasoundmoney/docs/blob/main/direct-auction-connections.md).

On a high level, our relay tries to identify which relay instance would have the lowest latency connection to a proposer and route proposer requests for headers there. In simple terms, a proposer next to the US instance would not see bids from the EU instance.

## Co-locating
Our instances run on OVH bare metal machines in their VIN (Vint Hill) data center. You can find their services on offer over on us.ovhcloud.com . OVH also maintains a dashboard showing the latency to many other data centers. The closest popular one being [AWS us-east-1](https://was1-vin.smokeping.ovh.net/smokeping??&target=USA.AS16509-us-east-1&) in Virginia, 2.5ms away.
