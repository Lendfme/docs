_**Notice: Our API document is still being edited, so if you want to use our API, please pay attention to our changes, the final stable version will be determined in 45 days from now on(2019-12-20).**_

<hr>

## LendfMe API Document

### Introduction


Welcome to LenfMe's API Documentation!

We provide some API interfaces to query infomation about Lendf.Me MoneyMarket, you can get information about

 - market(Asset information)
 - accounts(Details about all users who have borrowed)
 - liquidation(All liquidation transaction)

If you have any questions or feedback, you can open an issue at [our github repo](https://github.com/Lendfme/docs/issues) or come chat with us on our [Telegram](https://t.me/dforcenet).

## Public Rest API

### List Markets

Returns all markets.

**HTTP Request**

```
GET https://api.lendf.me/v1/info?data=markets
```

Response:

```
{
  "markets": {
    "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549": {
      "totalSupply": "1380784.707767959620073681",
      "supplyAPR": "0.0819639378039456",
      "totalBorrows": "1141769.522567662438590368",
      "borrowAPR": "0.1005003646328448",
      "price": "7911392405063291"
    },
    "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2": {
      "totalSupply": "23807.064736629981159866",
      "supplyAPR": "0.0004588174195776",
      "totalBorrows": "1151.894501677839263837",
      "borrowAPR": "0.009676580412672",
      "price": "1000000000000000000"
    },
    "0xdAC17F958D2ee523a2206206994597C13D831ec7": {
      "totalSupply": "611365.006527",
      "supplyAPR": "0.0617003644027392",
      "totalBorrows": "476803.516945",
      "borrowAPR": "0.0879158387928672",
      "price": "7909515146721505000000000000"
    }
  },
  "collateralRatio": "1250000000000000000",
  "originationFee": "500000000000000",
  "liquidationDiscount": "100000000000000000"
}
```

### Liquidation history

Returns all liquidation history.

**HTTP Request**

```
GET https://api.lendf.me/v1/info?data=liquidateBorrow&address=all
```

Response:

```
{
  "data": [
    {
      "transactionHash": "0x5157e2177e34e6baf160a1bedd053f36a031d5936c0e60eeaf65a5a05379d0cb",
      "blockNumber": 9125606,
      "targetAccount": "0xEe7e46D15DF85D113D4f8B80A4d748c20Fa625AA",
      "liquidator": "0x932906251479106D96904184aA4985C1a291B35d",
      "assetBorrow": {
        "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
        "symbol": "USDx",
        "decimal": 18,
        "borrowBalanceBefore": "0.042607990732737117",
        "borrowBalanceAccumulated": "0.042746035328543172",
        "amountRepaid": "0.01",
        "borrowBalanceAfter": "0.032746035328543172"
      },
      "assetCollateral": {
        "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
        "symbol": "WETH",
        "decimal": 18,
        "collateralBalanceBefore": "0.000357795210611359",
        "collateralBalanceAccumulated": "0.000357797776186854",
        "amountSeized": "0.000089920706286274",
        "collateralBalanceAfter": "0.00026787706990058"
      }
    }
  ],
  "size": 1,
  "total": 1
}
```

### Get an account liquidation history

Returns an account liquidation history by address.

**HTTP Request**

```
GET https://api.lendf.me/v1/info?data=liquidateBorrow&address=0xEe7e46D15DF85D113D4f8B80A4d748c20Fa625AA
```

Response:

```
{
  "data": [
    {
      "transactionHash": "0x5157e2177e34e6baf160a1bedd053f36a031d5936c0e60eeaf65a5a05379d0cb",
      "blockNumber": 9125606,
      "targetAccount": "0xEe7e46D15DF85D113D4f8B80A4d748c20Fa625AA",
      "liquidator": "0x932906251479106D96904184aA4985C1a291B35d",
      "assetBorrow": {
        "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
        "symbol": "USDx",
        "decimal": 18,
        "borrowBalanceBefore": "0.042607990732737117",
        "borrowBalanceAccumulated": "0.042746035328543172",
        "amountRepaid": "0.01",
        "borrowBalanceAfter": "0.032746035328543172"
      },
      "assetCollateral": {
        "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
        "symbol": "WETH",
        "decimal": 18,
        "collateralBalanceBefore": "0.000357795210611359",
        "collateralBalanceAccumulated": "0.000357797776186854",
        "amountSeized": "0.000089920706286274",
        "collateralBalanceAfter": "0.00026787706990058"
      }
    }
  ],
  "size": 1,
  "total": 1
}
```

### List borrowed accounts

Returns all accounts who have borrowed asset.

**HTTP Request**

```
GET https://api.lendf.me/v1/account?pageNumber=1&pageSize=50
```

Response:

```
{
  "accounts": [{
    "address": "0xEe7e46D15DF85D113D4f8B80A4d748c20Fa625AA",
    "total_supply_weth": "267877726545115",
    "total_borrow_weth": "259175084009486",
    "shortfall_weth": "56091128466743",
    "collateral_rate": "0.008177043018522064",
    "borrow": [{
      "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
      "amount": "32759730618799134"
    },
    {
      "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
      "amount": "0"
    },
    {
      "asset": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
      "amount": "0"
    }],
    "supply": [{
      "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
       "amount": "0"
    },
    {
      "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
      "amount": "267877726545115"
    },
    {
      "asset": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
      "amount": "0"
    }]
  }],
  "request": {
    "block_number": 9134359,
    "page_size": 50,
    "current_page_number": 1,
    "total_size": 1,
    "total_page_number": 1
  }
}
```

### Get max liquidation amount

Returns max liquidation amount by _**targetAccount**_, _**assetBorrow**_, _**assetCollateral**_

**HTTP Request**

```
GET https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549
```

Response:

```
{
  "targetAccount": 0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea,
  "assetBorrow": 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2,
  "assetCollateral": 0xeb269732ab75A6fD61Ea60b06fE994cD32a83549,
  "maxClosed": "1000000000000000000"
}
```
