# Bid Forwarding

By default, unless you've asked us not to, we gossip all top bids between our auction instances. It's also possible to control this on a per-submission basis using the `SHARE` http header. Possible values are:

- `all`
- `none`
- `us`
- `europe`
