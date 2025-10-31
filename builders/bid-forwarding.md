# bid forwarding

on the auction level, unless you've asked us not to, we gossip all top bids between our auction instances. it is also possible to control this on a per-submission basis using the `SHARE` http header. possible values are `["all", "none", "us", "europe"]`.

although this means bids submitted to one auction might not show up in others, including their top bid websocket, this does not mean bids submitted to our rbx auction in the eu are only offered to proposers which call to an edge server of ours in or close to rbx. the closest global edge server to the proposer will receive a stream of bids from every auction. whenever the latest possible moment to respond to a proposer arrives, it will consider the freshest bid received by every builder, and reply with the highest among them. this has two important consequences:

1. the highest global bid wins.
2. for builders which submit to multiple auctions, it is not possible a more stale, high, bid wins, when a newer, lower, bid is globally winning also. cancellations are notable edge case should your builder want to entirely cancel, not replace, their freshest bid.
