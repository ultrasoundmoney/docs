# rate limits

by default unknown builders suffer several concrete penalties compared to seasoned builders:

1. they are rate limited.
2. their bids need to pass simulation before becoming eligible to win. this can be overcome by the builder turning on [optimistic-relaying-builder-guide.md](optimistic-relaying-builder-guide.md "mention").
3. their bids are larger, and take longer to download and process than optimized [optimistic-v3.md](optimistic-v3.md "mention")or [dehydrated bids](https://docs.titanrelay.xyz/builders/builder-integration#block-deltas). we're developing an offering to help builders submit bids more efficiently. contact [smilingalex](https://t.me/smilingalex) if you're interested.

to overcome your bids being rate-limited [ask us to issue you an API token](https://t.me/ultrasoundrelay). this'll unlock limits entirely, right away.

in the future, we plan to limit rates even for token-identified-bids to a level that is virtually free at a byte-submission-rate comparable to top builders. although top builders submit a lot, they also tend to be quite efficient. as processing bids isn't free, we may therefore ask some fee depending on how far your use exceeds that of the average top builder.

