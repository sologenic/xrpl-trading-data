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
- Trades are available for any asseet by the following issuers. (This list will expand in the future, but for any additions please email: info@sologenic.org)

```yaml
- "rsoLo2S1kiGeCcn6hCUXVrCpGMWLrRrLZz" # Sologenic(SOLO)
- "rhub8VRN55s94qWKDv6jmDy1pUykJzF3wq" # Gatehub USD, EUR
- "rchGBxcD1A1C2tdxF6papQYZ8kjRKMYcL" # Gatehub BTC (Bitcoin)
- "rcA8X3TVMST1n3CJeAdGk1RdRCHii7N2h" # Gatehub ETH (Ethereum)
- "r3ttJ41YnMrKiLqGUXJpQE8urqyMGjC8vE" # Gatehub QAU (Gold Gram)
- "rDAN8tzydyNfnNf2bfUQY6iR96UbpvNsze" # Gatehub ETC (Ethereum Classic)
- "rcyS4CeCZVYvTiKcxj6Sx32ibKwcDHLds" # Gatehub BCH (Bitcoin Cash)
- "rcXY84C4g14iFp6taFXjjQGVeHqSCh9RX" # Gatehub DSH (DASH)
- "rckzVpTnKpP4TJ1puQe827bV3X4oYtdTP" # Gatehub REP (Augur)
- "rvYAfWj5gh67oV6fW32ZzP3Aw4Eubs59B" # Bitstamp USD,EUR,JPY,GBP,CHF,AUD,BTC
- "rCSCManTZ8ME9EoLrSHHYKW8PPwWMgkwr" # CasinoCoin CSC
- "rXTKdHWuppSjkbiKoEv53bfxHAn1MxmTb" # Kudos XTK
- "raEQc5krJ2rUXyi6fgmUAf63oAXmF7p6jp" # Allvor ALV
- "rNdwi8ain5ibXNB9A7H3zzKtSxgVzAqqAe" # Unknown GCB
- "razqQKzJRdB4UxFPWf5NEpEG3WMkmwgcXA" # Unknown CNY
- "rKiCet8SdvWxPXnAgYarFUXMh1zCPz432Y" # Unknown CNY
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown JPY
- "rpJZ5WyotdphojwMLxCr2prhULvG3Voe3X" # Unknown BTC
- "rMwjYedjc7qqtKYVLiAccJSmCwih4LnE2q" # Unknown USD
- "r94s8px6kSw1uZ1MV98dhSRTvc6VMPoPcN" # Unknown JPY
- "rnuF96W4SZoCJmbHYBFoJZpR8eCaxNvekK" # Unknown CNY
- "rBfVgTnsdh8ckC19RM8aVGNuMZnpwrMP6n" # Unknown GCB
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown BTC
- "rfNZPxoZ5Uaamdp339U9dCLWz2T73nZJZH" # Unknown BRL
- "rKiCet8SdvWxPXnAgYarFUXMh1zCPz432Y" # Unknown XLM
- "rMAz5ZnK73nyNUL4foAvaxdreczCkG3vA6" # Unknown JPY
- "rUkMKjQitpgAM5WTGk79xpjT38DEJY283d" # Unknown KRW
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown ADA
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown RJP
- "r3ADD8kXSUKHd6zTCKfnKT3zV9EZHjzp1S" # Unknown CAD
- "rpG9E7B3ocgaKqG7vmrsu3jmGwex8W4xAG" # Unknown XAG
- "rPKSvQ1qFAksr7hzk2wC2xtqSqFbxP3wvg" # Unknown XNF
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown ETH
- "rBfVgTnsdh8ckC19RM8aVGNuMZnpwrMP6n" # Unknown JCB
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown DOG
- "rM8199qFwspxiWNZRChZdZbGN5WrCepVP1" # Unknown CNY
- "rHZP1pA3THovFqvwgtM2tnJWrS54hSrDiG" # Unknown WTC
- "rMFG3vAYdAwjYxG21qf2CyZPWjgo5apPjF" # Unknown BCC
- "rBfVgTnsdh8ckC19RM8aVGNuMZnpwrMP6n" # Unknown CNY
- "rDKcJtUW5b8URJB5AVb41jkTeETfXWwahe" # Unknown JPY
- "rUXUGrsSgqya9CxiA4oxzSgNmDhsb6qZ6c" # Unknown GCB
- "rN2hcNiNwptpZuu64VKQbRVJnga8sUb7vr" # Unknown GCB
- "rfYv1TXnwgDDK4WQNbFALykYuEBnrR4pDX" # Unknown BTC
- "rKiCet8SdvWxPXnAgYarFUXMh1zCPz432Y" # Unknown STR
- "ruazs5h1qEsqpke88pcqnaseXdm6od2xc" # Unknown GKO
- "rJRi8WW24gt9X85PHAxfWNPCizMMhqUQwg" # Unknown JPY
- "rMUVZiUKrcZNqfpgNJRqQjpatGCPZSdrB8" # Unknown JED
- "rKiCet8SdvWxPXnAgYarFUXMh1zCPz432Y" # Unknown ULT
- "rEYD5k6kZPARQbKfB9GpiNPDY93kRQgPKj" # Unknown RBJ
- "rvYAfWj5gh67oV6fW32ZzP3Aw4Eubs59B" # Unknown EUR
- "rHSFu2BEV15GY3QmDeFznRXqgXkMhihccC" # Unknown BTC
- "r3bZUzUSpxh1r5L4Q3K66gtq4CMPA6pvy1" # Unknown GCB
- "rP1aDuhUy2wcRewg8v9ff17TnYajNg3KHj" # Unknown GCB
- "rLTSuhKSNaWvGHyRcnTSjms3iFew345SdG" # Unknown RSC
- "rDwBC8aUGtNwkES6dwQZ16pBeokHYv6Ut7" # Unknown GCB
- "rJHygWcTLVpSXkowott6kzgZU6viQSVYM1" # Unknown BTC
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown USD
- "rGErfV2Cv1oSZsN5bWT98yHnQ86XpgMExi" # Unknown GCB
- "rcoef87SYMJ58NAFx7fNM5frVknmvHsvJ" # Unknown XAU
- "rNeTgsaD6UVtCGdGqNjcsGTYk1dLDBVfyW" # Unknown GCB
- "rwdRX4jgRB5rZkbbbG7ezVTBKdBNKVrtpR" # Unknown KRW
- "raxYySYxFrBZvC1MQPRxfLeqRDgW1nirmz" # Unknown GCB
- "r4dYV67qy9fhMTscAUxb1GunLXs3BPu2bC" # Unknown GCB
- "rp2PaYDxVwDvaZVLEQv7bHhoFQEyX1mEx7" # Unknown USD
- "rNx3ty1w48iAa4JDfqhZk6bphwXxvCEtZP" # Unknown XEC
- "rBfVgTnsdh8ckC19RM8aVGNuMZnpwrMP6n" # Unknown VRB
- "r9ZFPSb1TFdnJwbTMYHvVwFK1bQPUCVNfJ" # Unknown JPY
- "rNPRNzBB92BVpAhhZr4iXDTveCgV5Pofm9" # Unknown USD
- "r9qouqdcygAWyoFGPxwMN3LoJkpHJHoCBd" # Unknown JUD
- "rScheetz28HPqwAo3sxCtqKxU5TPWbLEf" # Unknown SCZ
- "r4H3F9dDaYPFwbrUsusvNAHLz2sEZk4wE5" # Unknown ICE
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown BCC
- "rErDWeG8388YhWfr9cGAk2RqJZXsY8W5ag" # Unknown GCB
- "rMXmVZqMy3zVaKyUwauxjWS6ZUqSjkq3g3" # Unknown GCB
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown LTC
- "rJyiW2QS9ijkSUgF14478b1iJLg2pVrZHm" # Unknown GCB
- "rpoGV4pdh1K57QBFSReDrAQ3ji8uUX1WuJ" # Unknown GCB
- "rB7ktfqFpwABgdPcrXzq5mqq4Eia48ttLw" # Unknown JKC
- "rDv7EtdTsvbkrM1eGwRWchUCM6dj6SztBf" # Unknown GCB
- "rDkEmeEaeACwEaW3D5f2AegCoBy9BGT8j9" # Unknown GCB
- "rKxKhXZCeSDsbkyB8DVgxpjy5AHubFkMFe" # Unknown BTC
- "rJWP64zDHrSszr9ZgMWemWUWLZz2AKCFw7" # Unknown GCB
- "rPaLJYM32LaVYdxNKKAjcYVC1zNz2YyXX7" # Unknown GCB
- "rBfVgTnsdh8ckC19RM8aVGNuMZnpwrMP6n" # Unknown USD
- "rKiCet8SdvWxPXnAgYarFUXMh1zCPz432Y" # Unknown FMM
- "rNPRNzBB92BVpAhhZr4iXDTveCgV5Pofm9" # Unknown ILS
- "rE6CBQiJ5JvCeVJj6cCDQoFBTgXMnGjzF" # Unknown GCB
- "r9LDdYfNWCnHSdQnfY48pk4EZBjWNm7mh3" # Unknown GCB
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown STR
- "rDLqfYDWz9qxzbW2fZ35ppMzhPwD6RuyAd" # Unknown GCB
- "rEYD5k6kZPARQbKfB9GpiNPDY93kRQgPKj" # Unknown RIP
- "rp2PaYDxVwDvaZVLEQv7bHhoFQEyX1mEx7" # Unknown CCK
- "rNPRNzBB92BVpAhhZr4iXDTveCgV5Pofm9" # Unknown BTC
- "rfYv1TXnwgDDK4WQNbFALykYuEBnrR4pDX" # Unknown LTC
- "rsuZEXkKjBtYKwuNZ6eiEf7F3UZd8mVPFj" # Unknown GCB
- "rLJq8hchoS4W96iLKRMFmgLC2RzBHPUK3" # Unknown GCB
- "rn3z8b23oTsuSFGPt23oNqWyfDsaD6XMxy" # Unknown GCB
- "rrh7rf1gV2pXAoqA8oYbpHd8TKv5ZQeo67" # Unknown XAU
- "rLEsXccBGNR3UPuPu2hUXPjziKC3qKSBun" # Unknown USD
- "rM1HyGYdnUunkHqsQhCkCsxYkq8pAcq9BP" # Unknown GCB
- "rpQ5GMdEr2z9y14mTDWmgaQT7yBJP2diDD" # Unknown CNY
- "rLEsXccBGNR3UPuPu2hUXPjziKC3qKSBun" # Unknown EUR
- "ra9eZxMbJrUcgV8ui7aPc161FgrqWScQxV" # Unknown NXT
- "rB9XUkc3UEsBbt8h78xnQmnHtdzcPvdxix" # Unknown GCB
- "rp8uNQFUaDZUn4VuQvYRZVW3sM6YZsNvzC" # Unknown GCB
- "rEVxaqydnDTqiLC6ivwEAAG4j21zS2LuLe" # Unknown XPY
- "rLEsXccBGNR3UPuPu2hUXPjziKC3qKSBun" # Unknown BTC
- "rsP3mgGb2tcYUrxiLFiHJiQXhsziegtwBc" # Unknown NZD
- "rHZP1pA3THovFqvwgtM2tnJWrS54hSrDiG" # Unknown CTC
- "rPxU6acYni7FcXzPCMeaPSwKcuS2GTtNVN" # Unknown KRW
- "ra9eZxMbJrUcgV8ui7aPc161FgrqWScQxV" # Unknown DOG
- "rfK9co7ypx6DJVDCpNyPr9yGKLNer5GRif" # Unknown PCV
- "rGvWKKaVboTqY6GwP7afhDkYZ5qQMTvora" # Unknown YOU
- "r9wx4BoPPDtNYMkEY7Xj5bJK25JfyNwSZ" # Unknown RPS
- "rHKTzLQjXo7JM7JrKEZo2jyDtC7iqS6NJp" # Unknown XMF
- "rGNq2BwoMPoeTKaV65h46UfmvbrvoqpYCt" # Unknown GCB
- "rDRTbWrBREjpuo3qBVKTzjuxZqgzejbs8G" # Unknown GCB
- "rPA73XSVQ3UEz7gSixREhMzBMtF4RGZPie" # Unknown FRX
- "rHoUTGMxWKbrTTF8tpAjysjpu8PWrbt1Wx" # Unknown BIG
- "rVPpPfsbizsEoBXrk7mpHzb42R1w2LLwj" # Unknown BTC
- "rn5Ttyhd89VEQMeVhuSeAEJg41UH8zGHxa" # Unknown ONC
- "rwYWDtCRuWXjhJhrAy47HKbTVXbuQ4FMcM" # Unknown KRW
- "rnfJHRvhg1gpZeuf5HXyJWqHYj2qBVec6b" # Unknown BTC
- "r9Dr5xwkeLegBeXq6ujinjSBLQzQ1zQGjH" # Unknown XAU
- "rG6Fzwkovcg8dFiQ97fhujcbgPy1D3qyjh" # Unknown IOU
- "ra9eZxMbJrUcgV8ui7aPc161FgrqWScQxV" # Unknown USD
- "r9Dr5xwkeLegBeXq6ujinjSBLQzQ1zQGjH" # Unknown USD
- "rsP3mgGb2tcYUrxiLFiHJiQXhsziegtwBc" # Unknown BTC
- "rhLACkoYW7oMHf7nY7bHFrQbdcPEbdNt9U" # Unknown GCB
- "rsP3mgGb2tcYUrxiLFiHJiQXhsziegtwBc" # Unknown USD
- "rJXGFDrN5h3ooiCSsupPXhg81QgGtUQmeJ" # Unknown WEC
- "rNJ9mWvuP7iY9cMoG5YHRWF1eR5MJbNrcq" # Unknown GCB
- "rf6i9v2TT4zwyvRTbXTi9vD1te9sEyDVjK" # Unknown XCC
- "rB3gZey7VWHYRqJHLoHDEJXJ2pEPNieKiS" # Unknown ETC
- "rPDXxSZcuVL3ZWoyU82bcde3zwvmShkRyF" # Unknown USD
- "rh5sJ4ejSvyXGki5bgMJ7uzH1LpGqB3vGE" # Unknown MRH
- "rJRi8WW24gt9X85PHAxfWNPCizMMhqUQwg" # Unknown RMJ
- "rw7tWKbDoxRcGLnPAFUGHYJDGKweqgmseB" # Unknown GCB
- "rKUxrnNCpXCXAz49Vhtoo6VLfsV7yxU7x3" # Unknown FLC
- "rHKTzLQjXo7JM7JrKEZo2jyDtC7iqS6NJp" # Unknown XMG
- "rJXGFDrN5h3ooiCSsupPXhg81QgGtUQmeJ" # Unknown JPY
- "rsP3mgGb2tcYUrxiLFiHJiQXhsziegtwBc" # Unknown STR
- "rKCiRUUVHc5NFjrYboZsixnexTHGJEusQc" # Unknown GCB
- "rUx4xgE7bNWCCgGcXv1CCoQyTcCeZ275YG" # Unknown WCG
- "rrh7rf1gV2pXAoqA8oYbpHd8TKv5ZQeo67" # Unknown XAU
- "rUFcQnmGYEuvJVJnZy8QBc9ouKJbpvK7QU" # Unknown JPY
- "r4sYcbdi4oE18FhVYWEhXa1AEe21XGR39z" # Unknown XBW
- "rGYYWKxT1XgNipUJouCq4cKiyAdq8xBoE9" # Unknown CNY
- "rnuF96W4SZoCJmbHYBFoJZpR8eCaxNvekK" # Unknown BTC
- "ra9eZxMbJrUcgV8ui7aPc161FgrqWScQxV" # Unknown RDD
- "rJ1adrpGS3xsnQMb9Cw54tWJVFPuSdZHK" # Unknown CNY
- "rMLtFZE6ZvLkiRet8Bncs8EZEWQYXyXixN" # Unknown USD
- "r9Dr5xwkeLegBeXq6ujinjSBLQzQ1zQGjH" # Unknown SGD
- "r9Dr5xwkeLegBeXq6ujinjSBLQzQ1zQGjH" # Unknown XAG
- "rGvWKKaVboTqY6GwP7afhDkYZ5qQMTvora" # Unknown LIK
- "ra9eZxMbJrUcgV8ui7aPc161FgrqWScQxV" # Unknown VTC
- "rDiocag1uC1m59TqWdNR8ZnmMUjSamD1rQ" # Unknown WTR
- "rfqspG4S6H3NLYGecx3FHHYjPeYSNCdApf" # Unknown AGC
- "rUSrgqXVFEw5ZaruTqiDrXDecRuGnhFqu8" # Unknown ZPR
- "razqQKzJRdB4UxFPWf5NEpEG3WMkmwgcXA" # Unknown BTC
- "rBycsjqxD8RVZP5zrrndiVtJwht7Z457A8" # Unknown GBP
- "rJHygWcTLVpSXkowott6kzgZU6viQSVYM1" # Unknown LTC
- "rPDXxSZcuVL3ZWoyU82bcde3zwvmShkRyF" # Unknown BTC
- "rLbppnUciWQ2AfLkcdTfBwb1yh97Kc9cfx" # Unknown RMB
```

#### Testnet

- Trading data is recorded from [ledger number 19000000](https://xrpscan.com/ledger/19000000)
- Trades are available for any asseet by the following issuers. (This list will expand in the future, but for any additions please email: info@sologenic.org)

```yaml
- "rD9W7ULveavz8qBGM1R5jMgK2QKsEDPQVi" # XRP-toolkit issuer
- "rHZwvHEs56GCmHupwjA4RY7oPA3EoAJWuN" # New SOLO testnet issuer
- "rHZwvHEs56GCmHupwjA4RY7oPA3EoAJWuN" # Legacy SOLO testnet issuer
```
