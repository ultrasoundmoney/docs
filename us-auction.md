# US Auction

We've started experimimenting with running a US relay. Currently we only accept submissions from "directly connected builders" there. To become one follow the steps in the [direct-auctions-connection guide](https://github.com/ultrasoundmoney/docs/blob/main/direct-auction-connections.md).

On a high level, our relay tries to identify which relay instance would have the lowest latency connection to a proposer and route proposer requests for headers there. In simple terms, a proposer next to the US instance would not see bids from the EU instance.
