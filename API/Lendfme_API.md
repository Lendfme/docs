_**Notice: Our API document is still being edited, so if you want to use our API, please pay attention to our changes, the final stable version will be determined in 45 days from now on(2019-12-20).**_

<hr>

## LendfMe API Document

### Introduction

Welcome to LenfMe's API Documentation!

We provide some API interfaces to query infomation about Lendf.Me Money Market, you can get information about

- market(Asset information)
- accounts(Details about all users who have borrowed)
- liquidation(All liquidation transactions)

If you have any questions or feedback, you can open an issue at [our github repo](https://github.com/Lendfme/docs/issues) or come chat with us on our [Telegram](https://t.me/dforcenet).

## Public Rest API

### Get List Markets

> get market information

**Url**

> [https://api.lendf.me/v1/info?data=markets](https://api.lendf.me/v1/info?data=markets)

**Request Method**

```
GET
```

**Request Parameters**

```
none
```

**Response Result**

| field               | type   | description                 |
| ------------------- | ------ | --------------------------- |
| markets             | object | List all assets information |
| collateralRatio     | string | Asset collateral ratio      |
| originationFee      | string | Borrowing fee               |
| liquidationDiscount | string | Liquidation discount        |

**_markets details_**

| name        | type   | description                               |
| ----------- | ------ | ----------------------------------------- |
| symbol      | string | The symbol of the asset                   |
| decimal     | int    | The number of decimals used for the asset |
| totalSupply | string | Total amount of this asset supplied       |
| supplyAPR   | string | Supply annualized interest rate           |
| totalBorrow | string | Total amount of this asset borrowed       |
| borrowAPR   | string | Borrow annualized interest rate           |
| price       | string | The price of the asset relative to ETH    |

**Example:**

**Request url**

> [https://api.lendf.me/v1/info?data=markets](https://api.lendf.me/v1/info?data=markets)

**Response:**

```
{
  "markets": {
    "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549": {
      "symbol": "USDx",
      "decimal": 18,
      "totalSupply": "1375683.923234411593509296",
      "supplyAPR": "0.08299309236552",
      "totalBorrows": "1141769.522567662438590368",
      "borrowAPR": "0.1013605489469088",
      "price": "0.007607108081791626"
    },
    "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2": {
      "symbol": "WETH",
      "decimal": 18,
      "totalSupply": "23807.064736629981159866",
      "supplyAPR": "0.0000000003237696",
      "totalBorrows": "0.969722037834154025",
      "borrowAPR": "0.0000081462092256",
      "price": "1.00"
    }
  },
  "collateralRatio": "1250000000000000000",
  "originationFee": "500000000000000",
  "liquidationDiscount": "100000000000000000"
}
```

**Error message**

> none

### Liquidation history

> Returns all liquidation history.

**Url**

> [https://api.lendf.me/v1/info?data=liquidateBorrow](https://api.lendf.me/v1/info?data=liquidateBorrow)

**Request Method**

```
GET
```

**Request Parameters**

| Parameter | value                                | description                                                              |
| --------- | ------------------------------------ | ------------------------------------------------------------------------ |
| address   | User account address(default is all) | The acount you want to get liquidation history(case-insensitive letters) |

**Return Result**

| field | type  | description                                                           |
| ----- | ----- | --------------------------------------------------------------------- |
| data  | array | Liquidate information list                                            |
| size  | int   | Total number of liquidation history for a user(default for all users) |
| total | int   | Total number of liquidation history for all users                     |

**_data_**

| name            | type   | description                                   |
| --------------- | ------ | --------------------------------------------- |
| transactionHash | string | Transaction hash                              |
| blockNumber     | int    | When the transaction was confirmed            |
| targetAccount   | string | Liquidated address                            |
| liquidator      | string | Liquidator address                            |
| assetBorrow     | object | Asset information borrowed by liquidated user |
| assetCollateral | object | Asset information supplied by liquidated user |

**_assetBorrow_**

| name                     | type   | description                                                           |
| ------------------------ | ------ | --------------------------------------------------------------------- |
| asset                    | string | Asset address borrowed by liquidated user                             |
| symbol                   | string | The symbol of the asset                                               |
| decimal                  | int    | The number of decimals used for the asset                             |
| borrowBalanceBefore      | string | Borrowing amount before liquidation                                   |
| borrowBalanceAccumulated | string | Total amount of principal and interest of the loan before liquidation |
| amountRepaid             | string | The number of asset was repaid                                        |
| borrowBalanceAfter       | string | Borrowing amount after liquidation                                    |

**_assetCollateral_**

| name                         | type   | description                                                             |
| ---------------------------- | ------ | ----------------------------------------------------------------------- |
| asset                        | string | Asset address supplied by liquidated user                               |
| symbol                       | string | The symbol of the asset                                                 |
| decimal                      | int    | The number of decimals used for the asset                               |
| collateralBalanceBefore      | string | Supply amount before liquidation address                                |
| collateralBalanceAccumulated | string | Total amount of principal and interest of the supply before liquidation |
| amountSeized                 | string | The number of collateral seized by liquidator                           |
| collateralBalanceAfter       | string | Supply amount after liquidation                                         |

**Example**

**Request url**

> [https://api.lendf.me/v1/info?data=liquidateBorrow&address=all]()

**Response:**

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

**Error message**

> none

### List borrowed accounts

> Get accounts list have borrowed asset.

**Url**

> [https://api.lendf.me/v1/account](https://api.lendf.me/v1/account)

**Request Method**

```
GET
```

**Request Parameters**

| Parameters | value       | description                                                                                                      |
| ---------- | ----------- | ---------------------------------------------------------------------------------------------------------------- |
| pageNumber | Page number | The number of page to get                                                                                        |
| pageSize   | Page size   | The total number of accounts list at the current page(the parameter cannot be zero and less than or equal to 50) |

**Return Result**

| field    | type   | description                                 |
| -------- | ------ | ------------------------------------------- |
| accounts | array  | List of borrowed account information        |
| request  | object | Information about page number and page size |

**_accounts_**

| name            | type   | description                          |
| --------------- | ------ | ------------------------------------ |
| address         | string | Borrowing account address            |
| totalSupplyWeth | string | Total Supply in ETH                  |
| totalSupplyUSD  | string | Total supply value in US dollars     |
| totalBorrowWeth | string | Total borrow in ETH                  |
| totalBorrowUSD  | string | Total borrow value in US dollars     |
| shortfallWeth   | string | shortfall in ETH.                    |
| collateralRate  | double | Divide total supply and total borrow |
| borrow          | array  | Borrowing assets information         |
| supply          | array  | Suppling assets information          |

**_borrow_**

| name        | type   | description                               |
| ----------- | ------ | ----------------------------------------- |
| asset       | string | Borrowing asset address                   |
| symbol      | string | The symbol of the asset                   |
| decimal     | int    | The number of decimals used for the asset |
| oraclePrice | string | The asset price got from contract         |
| amount      | string | The amount of borrowed asset              |
| USDValue    | string | The USD value of borrowed asset           |
| price       | string | The price of borrowed asset in USD        |

**_supply_**

| name        | type   | description                               |
| ----------- | ------ | ----------------------------------------- |
| asset       | string | Suppling asset address                    |
| symbol      | string | The symbol of the asset                   |
| decimal     | int    | The number of decimals used for the asset |
| oraclePrice | string | The asset price got from contract         |
| amount      | string | The amount of supplied asset              |
| USDValue    | string | The USD value of supplied asset           |
| price       | string | The price of supplied asset in USD        |

**_request_**

| name            | type | description                        |
| --------------- | ---- | ---------------------------------- |
| blockNumber     | int  | Number of current blocks           |
| pageSize        | int  | Current page size                  |
| pageNumber      | int  | Current page number                |
| totalSize       | int  | Total number of account lists      |
| totalPageNumber | int  | Total number of pages by page size |

**Example**

**Request Url**

> [https://api.lendf.me/v1/account](https://api.lendf.me/v1/account)

**Response:**

```
{
  "accounts": [
    {
      "address": "0xA89BC2a9FE859A9367010429aCcfF192bCA33891",
      "totalSupplyWeth": "0.000028139995325289",
      "totalSupplyUSD": "0.003737272779151632",
      "totalBorrowWeth": "0.000030996561728117481242375236649517",
      "totalBorrowUSD": "0.004116653363111283",
      "shortfallWeth": "0.000010605706834857851552969045811896",
      "collateralRate": 0.9078424753079228,
      "borrow": [
        {
          "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
          "symbol": "USDx",
          "decimal": 18,
          "oraclePrice": "0.007529553497477599",
          "amount": "0.004116653363111283",
          "USDValue": "0.004116653363111283",
          "price": "1.00"
        }
      ],
      "supply": [
        {
          "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
          "symbol": "WETH",
          "decimal": 18,
          "oraclePrice": "1.00",
          "amount": "0.000028139995325289",
          "USDValue": "0.003737272779151632",
          "price": "132.810000000000010201"
        }
      ]
    }
  ],
  "request": {
    "blockNumber": 9150625,
    "pageSize": 10,
    "pageNumber": 1,
    "totalSize": 16,
    "totalPageNumber": 2
  }
}
```

**Error message**

> Returns error message

| Error name | Error message                            | description                                             |
| ---------- | ---------------------------------------- | ------------------------------------------------------- |
| Error      | You should provide a non-zero parameter! | Input parameter is zero or page size is greater than 50 |

### Get max liquidation amount

> Returns max liquidation amount by **_targetAccount_**, **_assetBorrow_**, **_assetCollateral_**

**Url**

> [https://api.lendf.me/v1/liquidate?targetAccount= 0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow= 0xeb269732ab75A6fD61Ea60b06fE994cD32a83549&assetCollateral= 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2](https://api.lendf.me/v1/liquidate?targetAccount= 0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow= 0xeb269732ab75A6fD61Ea60b06fE994cD32a83549&assetCollateral= 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2)

**Request Method**

```
GET
```

**Request Parameters**

| Parameters      | value                                           | description                                     |
| --------------- | ----------------------------------------------- | ----------------------------------------------- |
| targetAccount   | Liquidated address                              | Target account to be liquidated                 |
| assetBorrow     | Asset address borrowed by Liquidated users      | Target account borrowed asset address           |
| assetCollateral | Asset address collateralizedby Liquidated users | Target account supplyied asset contract address |

**Return result**

| name            | type   | description                                     |
| --------------- | ------ | ----------------------------------------------- |
| targetAccount   | string | Target account to be liquidated                 |
| assetBorrow     | string | Target account borrowed asset address           |
| assetCollateral | string | Target account supplyied asset contract address |
| maxClose        | object | The number of liquidation requested             |
| amountSeize     | object | The number of collateral seized by liquidator   |

**_maxClose_**

| name        | type   | description                               |
| ----------- | ------ | ----------------------------------------- |
| symbol      | string | The symbol of the asset                   |
| decimal     | int    | The number of decimals used for the asset |
| oraclePrice | string | The asset price got from contract         |
| amountRaw   | string | Original amount of borrowed asset         |
| amount      | string | The number of borrowed asset in ETH       |
| USDValue    | string | The USD value of borrowed asset           |
| price       | string | The price of borrowed asset in USD        |

**_amountSeize_**

| name        | type   | description                               |
| ----------- | ------ | ----------------------------------------- |
| symbol      | string | The symbol of the asset                   |
| decimal     | int    | The number of decimals used for the asset |
| oraclePrice | string | The asset price got from contract         |
| amountRaw   | string | Original amount of borrowed asset         |
| amount      | string | The number of borrowed asset in ETH       |
| USDValue    | string | The USD value of borrowed asset           |
| price       | string | The price of borrowed asset in USD        |

**Example**

**Request Url**

> [https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549](https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549)

**Response:**

```
{
  "targetAccount": "0xA89BC2a9FE859A9367010429aCcfF192bCA33891",
  "assetBorrow": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
  "assetCollateral": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
  "maxClose": {
    "symbol": "USDx",
    "decimal": 18,
    "oraclePrice": "7607108081791626",
    "amountRaw": "3362882932255269",
    "amount": "0.003362882932255269",
    "USDValue": "0.003362882932255269",
    "price": "1.00"
  },
  "amountSeize": {
    "symbol": "WETH",
    "decimal": 18,
    "oraclePrice": "1000000000000000000",
    "amountRaw": "28139995325285",
    "amount": "0.000028139995325285",
    "USDValue": "0.003699171225480665",
    "price": "131.456000000000001648"
  }
}
```

**Error message**

> Returns error message

| Error name | Error message                     | Error parameter | description                                    |
| ---------- | --------------------------------- | --------------- | ---------------------------------------------- |
| Error      | targetAccount Parameter error!!   | targetAccount   | The input parameter is not an Ethereum address |
| Error      | assetBorrow Parameter error!!     | assetBorrow     | The input parameter is not an Ethereum address |
| Error      | assetCollateral Parameter error!! | assetCollateral | The input parameter is not an Ethereum address |
