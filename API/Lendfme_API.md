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
  "markets": [
    {
      "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
      "symbol": "USDx",
      "decimal": 18,
      "blockNumber": 9168593,
      "totalSupplyRaw": "1446840642874118566820740",
      "totalSupply": "1446840.64287411856682074",
      "totalSupplyUSD": "1446840.64287411856682074",
      "supplyAPR": "0.0816262286380992",
      "totalBorrowRaw": "1195049071178813034553478",
      "totalBorrow": "1195049.071178813034553478",
      "totalBorrowUSD": "1195049.071178813034553478",
      "borrowAPR": "0.1002177881255232",
      "oraclePrice": "8039877793857533",
      "price": "1000000000000000000"
    },
    {
      "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
      "symbol": "WETH",
      "decimal": 18,
      "blockNumber": 9160112,
      "totalSupplyRaw": "25107117730666635275747",
      "totalSupply": "25107.117730666635275747",
      "totalSupplyUSD": "3122823.303340316237560959",
      "supplyAPR": "0.0019464442497216",
      "totalBorrowRaw": "2502219722037834154025",
      "totalBorrow": "2502.219722037834154025",
      "totalBorrowUSD": "311226.089027065826225967",
      "borrowAPR": "0.0199307180104992",
      "oraclePrice": "1000000000000000000",
      "price": "124380000000000005654"
    },
    {
      "asset": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
      "symbol": "USDT",
      "decimal": 6,
      "blockNumber": 9169941,
      "totalSupplyRaw": "853182047781",
      "totalSupply": "853182.047781",
      "totalSupplyUSD": "851607.279536158997068811",
      "supplyAPR": "0.0708765744479616",
      "totalBorrowRaw": "677537273038",
      "totalBorrow": "677537.273038",
      "totalBorrowUSD": "676286.702676080845382312",
      "borrowAPR": "0.0911099802445632",
      "oraclePrice": "8025038118931064000000000000",
      "price": "998154241232645785696091806436"
    },
    {
      "asset": "0x3212b29E33587A00FB1C83346f5dBFA69A458923",
      "symbol": "imBTC",
      "decimal": 8,
      "blockNumber": 9169757,
      "totalSupplyRaw": "9000000",
      "totalSupply": "0.09",
      "totalSupplyUSD": "645.608166560932032341",
      "supplyAPR": "0.002421904334976",
      "totalBorrowRaw": "1000500",
      "totalBorrow": "0.010005",
      "totalBorrowUSD": "71.770107849356944261",
      "borrowAPR": "0.0222320982162528",
      "oraclePrice": "576734529096256992900000000000",
      "price": "71734240728992448037940583539996"
    }
  ],
  "totalSupplyBalance": "43591.54875315287415181309463878830963442",
  "totalSupplyBalanceUSD": "5421916.833917154733482851",
  "totalBorrowBalance": "17548.107678001358448974879403073801649774",
  "totalBorrowBalanceUSD": "2182633.63298980906310602",
  "totalCollateralizationRatio": "2.484116780739844033",
  "userCount": 130,
  "collateralRatio": "1250000000000000000",
  "liquidationDiscount": "100000000000000000",
  "originationFee": "500000000000000"
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

> [https://api.lendf.me/v1/info?data=liquidateBorrow&address=all](https://api.lendf.me/v1/info?data=liquidateBorrow&address=all)

**Response:**

```
{
  "data": [
    {
      "transactionHash": "0x5782dbc0bdc750b2ce2f1e5b999af84b3e38049e60bca9b8967354548d5527f1",
      "blockNumber": 9154419,
      "targetAccount": "0x1Ff27A24A83AF6f99F1108f0E59946b183D63C96",
      "liquidator": "0x932906251479106D96904184aA4985C1a291B35d",
      "assetBorrow": {
        "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
        "symbol": "USDx",
        "decimal": 18,
        "borrowBalanceBefore": "0.001159121818411891",
        "borrowBalanceAccumulated": "0.001188125250888974",
        "amountRepaid": "0.000953282337615487",
        "borrowBalanceAfter": "0.000234842913273487"
      },
      "assetCollateral": {
        "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
        "symbol": "WETH",
        "decimal": 18,
        "collateralBalanceBefore": "0.000008456554990961",
        "collateralBalanceAccumulated": "0.000008456652785258",
        "amountSeized": "0.000008182681009574",
        "collateralBalanceAfter": "0.000000273971775684"
      }
    },
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
      "address": "0xEe7e46D15DF85D113D4f8B80A4d748c20Fa625AA",
      "totalSupplyWeth": "0.00000000000014923",
      "totalSupplyUSD": "0.000000000018561227",
      "totalBorrowWeth": "0.000012738548793649074834959278224376",
      "totalBorrowUSD": "0.001584420698954072",
      "shortfallWeth": "0.00001592318584283134354369909778047",
      "collateralRate": 1.1714835215326886e-8,
      "borrow": [
        {
          "asset": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
          "symbol": "USDx",
          "decimal": 18,
          "oraclePrice": "0.008039877793857533",
          "amount": "0.001584420698954072",
          "USDValue": "0.001584420698954072",
          "price": "1.00"
        }
      ],
      "supply": [
        {
          "asset": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
          "symbol": "WETH",
          "decimal": 18,
          "oraclePrice": "1.00",
          "amount": "0.00000000000014923",
          "USDValue": "0.000000000018561227",
          "price": "124.380000000000005654"
        }
      ]
    },
  ],
  "request": {
    "blockNumber": 9169935,
    "pageSize": 50,
    "pageNumber": 1,
    "totalSize": 1,
    "totalPageNumber": 1
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
  "targetAccount": "0xEe7e46D15DF85D113D4f8B80A4d748c20Fa625AA",
  "assetBorrow": "0xeb269732ab75A6fD61Ea60b06fE994cD32a83549",
  "assetCollateral": "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2",
  "maxClose": {
    "symbol": "USDx",
    "decimal": 18,
    "oraclePrice": "8039877793857533",
    "amountRaw": "16873843",
    "amount": "0.000000000016873843",
    "USDValue": "0.000000000016873843",
    "price": "1.00"
  },
  "amountSeize": {
    "symbol": "WETH",
    "decimal": 18,
    "oraclePrice": "1000000000000000000",
    "amountRaw": "149229",
    "amount": "0.000000000000149229",
    "USDValue": "0.000000000018561103",
    "price": "124.380000000000005654"
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
