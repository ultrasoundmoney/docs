# Direct auction connections

We offer the option to connect directly to our auction server, skipping a few network hops giving the fastest route for bids. To connect directly, you'll need to be whitelisted by us (contact us at https://t.me/ultrasoundrelay).

Once you've been whitelisted you can send requests directly to the IP of our auction server by including the following headers that we'll give you: `X-Builder-Id` and `X-Api-Token`. HTTPS is not supported. The following routes are available for direct connections:

- `/relay/v1/builder/blocks`
- `/relay/v1/builder/validators`
- `/eth/v1/builder/header`
- `/ws/v1`


You can verify that you've been whitelisted by:

```bash
curl -H 'X-Builder-Id: your_id' -H 'X-Api-Token: your_token' XXX.XXX.XXX:3000/relay/v1/builder/validators
```

## Downtime

Note that if you're connecting directly you'll receive `ECONNREFUSED` instead of `HTTP 503` if the auction service is unavailable.

## HTTP/2

For the lowest connection overhead, use HTTP/2 with keep-alive. Make sure your client supports HTTP/2 without TLS (h2c). The HTTP/1.1 to HTTP/2 upgrade might not work without TLS so you can skip the negotiation (equivalent to `curl --http2-prior-knowledge`).
