# GetHeader endpoint

To protect cancellable bids from polling, our header endpoint only returns one cancellable bid per slot, to the proposer. Everyone else gets the highest non-cancellable bid (floor bid). If you want to know what the top bid is, please use our [websocket endpoint](./top-bid-websocket.md).
