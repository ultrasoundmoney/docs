# Recent Registration

MEV relays have a standardized data API. One of the features offered is a way to check whether a proposer is registered with a relay. In the method standardized by Flashbots a relay will not record when a registration was last received if the registration is otherwise identical to the last. To some builders this information is valuable. Ultra Sound offers this information through a non-standard API.

https://relay.ultrasound.money/ultrasound/v1/data/registration?proposer_pubkey=0xb4c5032618b039bad9b835e7ed1f2a33b61155e09e67545daebdd8ccf9a94774ad3858268af6b859cb5e1ed86da478cf

The API expects a single query parameter, proposer_pubkey, indicating the proposer you'd like to see the registration for.

For a successful call the API returns a body with a field `data` with a single record.
Example:
```
{
    "data": {
        "last_registration_received_at": "2024-01-11T22:00:43.287Z",
        "last_registration_timestamp": "2024-01-11T22:00:43Z",
        "proposer_pubkey": "0xb4c5032618b039bad9b835e7ed1f2a33b61155e09e67545daebdd8ccf9a94774ad3858268af6b859cb5e1ed86da478cf"
    }
}
```

If a proposer is not registered, a 404 status will be returned and the value of the `data` field will be `null`.

Most bad requests will similarly have a JSON body with an `error` field. In rare error cases you may see no body, or a string body explaining the problem.
