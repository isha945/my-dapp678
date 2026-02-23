# Chainlink Price Feed

This project is configured to use **Chainlink Data Feeds** on `arbitrum`.

## Environment Variables

- `CHAINLINK_FEED_ADDRESS` – AggregatorV3Interface contract address
- `CHAINLINK_CHAIN` – Chain identifier (e.g. `ARBITRUM`, `ARBITRUM_SEPOLIA`)


## Usage

Use `useChainlinkPrice` from `@cradle/chainlink-price-feed` with your feed address and chain.
The hook reads `latestRoundData()` from the aggregator contract and formats the price using the feed's decimals (usually 8).
