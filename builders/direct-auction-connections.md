# Direct auction connections

We offer the option to connect directly to our auction server, skipping a few network hops giving the fastest route for bids. To connect directly, we want to whitelist you first (contact us at https://t.me/ultrasoundrelay).

When whitelisting we'll agree on a `X-Builder-Id` and `X-Api-Token` which need to be included in every request. You'll then be able to use the private address of the EU and US auction. HTTPS is not supported. The following routes are available for direct connections:

- `/relay/v1/builder/blocks`
- `/relay/v1/builder/validators`
- `/ws/v1`


You can verify that you've been whitelisted by:

```bash
curl -H 'X-Builder-Id: your_id' -H 'X-Api-Token: your_token' XXX.XXX.XXX:3000/relay/v1/builder/validators
```

## Downtime

Note that if you're connecting directly you'll receive `ECONNREFUSED` instead of `HTTP 503` if the auction service is unavailable.

## HTTP/2

For the lowest connection overhead, use HTTP/2 with keep-alive. Make sure your client supports HTTP/2 without TLS (h2c). The HTTP/1.1 to HTTP/2 upgrade might not work without TLS so you can skip the negotiation (equivalent to `curl --http2-prior-knowledge`).
