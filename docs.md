# XRP Ledger Trading Data API

## Summary

The XRP Ledger's built-in Decentralized Exchange is a low cost, fast and one of the most efficient exchanges on the Blockchain.

The team behind Sologenic has created a DEX interface [Sologenic DEX](https://sologenic.org/) that utilizes this technology to allow for XRP, SOLO and any other XRPL issued currency to be traded on-chain fast and seamlessly.

When building Sologenic DEX, the team at Sologenic used `Ripple Data API v2` which has been recently deprecated (https://xrpl.org/data-api.html). In spite of this, the Sologenic core team has created a free API that can be used by anyone to pull processed trading data from the XRP Ledger.

This API is currently being used in the Sologenic DEX production environment and both Mainnet and Testnet are currently supported.
We plan to add additional endpoints in the future. If you have any suggestions please feel free let us know by sending an email to info@sologenic.org

- This API is currently in **_BETA_** mode and minor changes in further releases will be compatible. For breaking changes we will use a versioning system to make sure existing users are not affected.

### Environments

Mainnet API root URL: `https://api.sologenic.org`

Testnet API root URL: `https://api-testnet.sologenic.org`

### Rate limiting

Current Trading data API rate limit is 100 rpm per IP. If you need a higher limit please email info@sologenic.org to be whitelisted.

## API documentation

### OHLC

`GET api/v1/ohlc`

#### Params

- `symbol` _required_ - symbol for which OHLC should be returned. NOTE: `symbol` should be urlsafe encoded.
- `period` _required_ - one of `["1m","3m","5m","15m","30m","1h","3h","6h","12h","1d","3d","1w"]`
- `from` _required_ - unix timestamp of OHLC start
- `to` _required_ - unix timestamp of OHLC end

**NOTE: API has limitation of 2000 points per request**

It means that you can request 2000 of 1m points (2000/60/24) ~1.3 days. Or 2000 of 1h points ~83.3 days etc.

#### Response

```json5
[
  [
    1611069600, // timestamp
    "5.54016620498615", // open
    "5.54016620498615", // high
    "5.54016620498615", // low
    "5.54016620498615", // close
    "1.9855", // volume
  ],
  [
    1611069660,
    "5.54016620498615",
    "5.54016620498615",
    "5.54016620498615",
    "5.54016620498615",
    "0",
  ],
]
```

#### Example

```bash
curl "https://api.sologenic.org/api/v1/ohlc?symbol=USD%2BrD9W7ULveavz8qBGM1R5jMgK2QKsEDPQVi%2FXRP&period=1m&from=1611007200&to=1611070980"
```

### Tickers

`POST api/v1/tickers/24h`

**NOTE: Since in most browsers URL size is limited to 2048 characters POST is preferred HTTP method to fetch tickers**

#### Params

- `symbols` _required max 20 symbols_ - an array of symbols for which tickers should be fetched.

#### Response

```json5
{
  "USD+rD9W7ULveavz8qBGM1R5jMgK2QKsEDPQVi/XRP": {
    open_time: 1611014400,
    open_price: "5.154639175257732",
    high_price: "5.54016620498615",
    low_price: "5.113524505284584",
    last_price: "5.54016620498615",
    volume: "13.049739333333827",
  },
  "XRP/USD+rD9W7ULveavz8qBGM1R5jMgK2QKsEDPQVi": {
    open_time: 1611014400,
    open_price: "0.1955598333334567",
    high_price: "0.1955598333334567",
    low_price: "0.1805",
    last_price: "0.1805",
    volume: "68",
  },
}
```

#### Example

**POST**

```bash
curl -X "POST" -H "Content-Type: application/json" "https://api.sologenic.org/api/v1/tickers/24h" --data '{"symbols": ["USD+rD9W7ULveavz8qBGM1R5jMgK2QKsEDPQVi/XRP", "XRP/USD+rD9W7ULveavz8qBGM1R5jMgK2QKsEDPQVi"]}'
```

### Trades

`GET api/v1/trades?symbol=`

#### Params

- `symbol` _optional_ - symbol for which recent trades should be returned. NOTE: `symbol` should be urlsafe encoded.
  NOTE: At least one of `symbol` or `account` is required
- `account` _optional_ - account for which recent trades should be returned. NOTE: At least one of `symbol` or `account` is required
- `before_id` _optional_ - if specified, returns only trades in which the ID is less than the specified value
- `after_id` _optional_ - if specified, returns only trades in which the ID is greater than the specified value
- `limit` _optional default 50 max 100_ - number of last trades to return

#### Response

```json5
[
  {
    id: 643521990000310000,
    txid: "A6D2D7ECF648F82703B62613E525A98D9342914A7CF39D8A0373DD05BBB5C9ED",
    symbol: "534F4C4F00000000000000000000000000000000+rsoLo2S1kiGeCcn6hCUXVrCpGMWLrRrLZz/XRP",
    buyer: "rNZf65LFq8jdWhQEo9faRYQHxn61kgK2yy",
    seller: "r3S8px1Qx6ctoQGv8puFwahoLWGjVZksQv",
    is_seller_taker: true,
    amount: "0.001",
    price: "1.15", // API returns precalculated price but preferable way is to calculate price on application side using `quote_amount` & `amount`.
    quote_amount: "0.00115",
    executed_at: "2021-06-18T11:33:12Z",
    time: "2021-06-18T11:33:12Z", // Compatibility. Use executed_at instead.
  },
  {
    id: 643521320000190000,
    txid: "5C8C9AD97C679D41B57BCCC24CD2DCCC85D30CAB0D53BBCD7AE94F7EEFE307AD",
    symbol: "534F4C4F00000000000000000000000000000000+rsoLo2S1kiGeCcn6hCUXVrCpGMWLrRrLZz/XRP",
    buyer: "rNZf65LFq8jdWhQEo9faRYQHxn61kgK2yy",
    seller: "r3S8px1Qx6ctoQGv8puFwahoLWGjVZksQv",
    is_seller_taker: true,
    amount: "0.001",
    price: "1.15",
    quote_amount: "0.00115",
    executed_at: "2021-06-18T11:29:02Z",
    time: "2021-06-18T11:29:02Z",
  },
]
```

#### Example

```bash
curl 'https://api.sologenic.org/api/v1/trades?symbol=534F4C4F00000000000000000000000000000000%2BrsoLo2S1kiGeCcn6hCUXVrCpGMWLrRrLZz%2FXRP&account=r3S8px1Qx6ctoQGv8puFwahoLWGjVZksQv&before_id=643521990000320000&after_id=643521320000180000'
```

### Limitations
#### Mainnet

- Trading data is recorded from [ledger number 54000000](https://xrpscan.com/ledger/54000000)


#### Testnet
- Trading data is recorded from [ledger number 19000000](https://xrpscan.com/ledger/19000000)

