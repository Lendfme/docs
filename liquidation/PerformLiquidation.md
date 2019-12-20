## How to perform liquidation on lendf.me

### About Lendf.Me

（lendf.me introduction）

[lendfMe](https://github.com/Lendfme)

### Contract Address

__MainNet:__

```
moneyMarket: 0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea
liquidator : 0x45b1953611da41f841def7dceff318f83409739d
```

__Rinkeby:__

```
moneyMarket: 0x2fd380b99e0c6ff16f569fb8214b40509f776764
liquidator : 0x8e8627fc8e2359fdc9492540d646c3f4d979a44a
```

_Notice: In order to support USDx contract, we make a little change in the Liquidator contract(line: 2796):_

```js
    function tokenAllowAll(address asset, address allowee) internal {
        EIP20Interface token = EIP20Interface(asset);

        if (token.allowance(address(this), allowee) != uint(-1)) // <--------Here!
            require(token.approve(allowee, uint(-1)), "FAILED_LIQUIDATE_ASSET_ALLOWANCE_FAILED");
    }
```

## Liquidation

### Step1: Get account list

For the contract of `lendf.me`, user account and related information are stored in a `mapping`, we can not get them directly, so, we provide an API: [https://api.lendf.me/v1/account?pageNumber=1&pageSize=50](https://api.lendf.me/v1/account?pageNumber=1&pageSize=50) to get all user information. When we try to visit this API, we will get the data like below:

```
{
  "accounts":[
    {
      "address": "0xcd63d37a4b28b55932611c7ab0c35ce63d729341",  //account address
      "total_supply_weth": "90239874928734",   //Total amount of collateral converted into WETH
      "total_borrow_weth": "80904234234",      //Total amount of borrowing converted into WETH
      "shortfall_weth": "3223094823",          //Total amount of assets gap converted into WETH
      "collateral_rate": "0.008177043018522064", //collateral rate, if it is more than 1.25, it means your account is safe
      "borrow":[ //borrowing detail
        {
          "asset": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",      //borrow asset contract address
          "amount": "1098739574"      //borrowing amount(including interest)
        },
        {
          "asset": "0xeb269732ab75a6fd61ea60b06fe994cd32a83549",     //borrow asset contract address
          "amount": "800904234234"   //borrowing amount(including interest)
        },
        {
          "asset": "0xdac17f958d2ee523a2206206994597c13d831ec7 ",     //borrow asset contract address
          "amount": "0"          //borrowing amount(including interest)
        }
      ],
      "supply":[ //collateral detail
        {
          "asset": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",     //collateral asset contract address
          "amount": "80904234234"     //collateral amount(including interest)
        },
        {
          "asset": "0xeb269732ab75a6fd61ea60b06fe994cd32a83549",     //collateral asset contract address
          "amount": "10098739574"    //collateral amount(including interest)
        },
        {
          "asset": "0xdac17f958d2ee523a2206206994597c13d831ec7 ",    //collateral asset contract address
          "amount": "0"          //collateral amount(including interest)
        }
      ]
    }
  ],
  "request":{
  "block_number":8353527,    //current block number
  "page_size":10,            //current page size(default:50)
  "current_page_number":1,   //current page number
  "total_size":100,           //total amount of borrowing account
  "total_page_number":2,     //total page number
  }
}
```
All data are sorted by `shortfall_weth` from big to small, first account has the largest asset gap. When user's value of `shortfall_weth` is bigger than zero, that means this account can be liquidated, in this situation, we should determine how to implement liquidation based on the account's collateral and borrowing, and whether we can get an extra profit through liquidation.

### Step2: Call the function `liquidateBorrow()`

```js
function liquidateBorrow(
  address targetAccount,    //target account to be liquidated
  address assetBorrow,      //target account borrowing asset contract address
  address assetCollateral,  //target account collateral asset contract address
  uint requestedAmountClose //number of liquidations requested
)
```

_Notice:The asset that user wants to liquidate must approve to the `Liquidator.sol` contract at first._

You can get `requestedAmountClose` by visiting our public API with variables `targetAccount`, `assetBorrow`, `assetCollateral`, such as: [https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549](https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549)

you will get data like below:

```
{
  "targetAccount": 0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea,
  "assetBorrow": 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2,
  "assetCollateral": 0xeb269732ab75A6fD61Ea60b06fE994cD32a83549,
  "maxClosed": "1000000000000000000"
}
```

- 1) You can copy the contract [Liquidator.sol](https://github.com/Lendfme/front_end_sc/blob/master/contracts/Liquidator.sol) and open it on the [Remix](https://remix.ethereum.org), initialize the contract by the contract address`0x45b1953611Da41F841def7DceFF318F83409739d(mainnet)`, and then call the function which is mentioned above.

- 2) You can use the popular JavaScript library [web3](https://github.com/ethereum/web3.js/) to call the contract function directly, you can also choose other scripting language.

- 3) You can visit [https://markets.lendf.me](https://market.lendf.me), then liquidate on the website, but you need to install extension MetaMask at first.

### Step3: Check the `liquidateBorrow()` return value

The function `liquidateBorrow()` contains many conditions checking, so if user does not meet some basically checking, your liquidation will fail, and at the same time you will get error index.

If your liquidation is successful, it will return a value of 0, if failed, it will return:

```js
uint(err), uint(info)
```

type `Error` defined as below:

```
enum Error {
    NO_ERROR,
    OPAQUE_ERROR,
    UNAUTHORIZED,
    INTEGER_OVERFLOW,
    INTEGER_UNDERFLOW,
    DIVISION_BY_ZERO,
    BAD_INPUT,
    TOKEN_INSUFFICIENT_ALLOWANCE,
    TOKEN_INSUFFICIENT_BALANCE,
    TOKEN_TRANSFER_FAILED,
    MARKET_NOT_SUPPORTED,
    SUPPLY_RATE_CALCULATION_FAILED,
    BORROW_RATE_CALCULATION_FAILED,
    TOKEN_INSUFFICIENT_CASH,
    TOKEN_TRANSFER_OUT_FAILED,
    INSUFFICIENT_LIQUIDITY,
    INSUFFICIENT_BALANCE,
    INVALID_COLLATERAL_RATIO,
    MISSING_ASSET_PRICE,
    EQUITY_INSUFFICIENT_BALANCE,
    INVALID_CLOSE_AMOUNT_REQUESTED,
    ASSET_NOT_PRICED,
    INVALID_LIQUIDATION_DISCOUNT,
    INVALID_COMBINED_RISK_PARAMETERS,
    ZERO_ORACLE_ADDRESS,
    CONTRACT_PAUSED
}
```

type `FailureInfo ` defined as below:

```
enum FailureInfo {
    ACCEPT_ADMIN_PENDING_ADMIN_CHECK,
    BORROW_ACCOUNT_LIQUIDITY_CALCULATION_FAILED,
    BORROW_ACCOUNT_SHORTFALL_PRESENT,
    BORROW_ACCUMULATED_BALANCE_CALCULATION_FAILED,
    BORROW_AMOUNT_LIQUIDITY_SHORTFALL,
    BORROW_AMOUNT_VALUE_CALCULATION_FAILED,
    BORROW_CONTRACT_PAUSED,
    BORROW_MARKET_NOT_SUPPORTED,
    BORROW_NEW_BORROW_INDEX_CALCULATION_FAILED,
    BORROW_NEW_BORROW_RATE_CALCULATION_FAILED,
    BORROW_NEW_SUPPLY_INDEX_CALCULATION_FAILED,
    BORROW_NEW_SUPPLY_RATE_CALCULATION_FAILED,
    BORROW_NEW_TOTAL_BALANCE_CALCULATION_FAILED,
    BORROW_NEW_TOTAL_BORROW_CALCULATION_FAILED,
    BORROW_NEW_TOTAL_CASH_CALCULATION_FAILED,
    BORROW_ORIGINATION_FEE_CALCULATION_FAILED,
    BORROW_TRANSFER_OUT_FAILED,
    EQUITY_WITHDRAWAL_AMOUNT_VALIDATION,
    EQUITY_WITHDRAWAL_CALCULATE_EQUITY,
    EQUITY_WITHDRAWAL_MODEL_OWNER_CHECK,
    EQUITY_WITHDRAWAL_TRANSFER_OUT_FAILED,
    LIQUIDATE_ACCUMULATED_BORROW_BALANCE_CALCULATION_FAILED,
    LIQUIDATE_ACCUMULATED_SUPPLY_BALANCE_CALCULATION_FAILED_BORROWER_COLLATERAL_ASSET,
    LIQUIDATE_ACCUMULATED_SUPPLY_BALANCE_CALCULATION_FAILED_LIQUIDATOR_COLLATERAL_ASSET,
    LIQUIDATE_AMOUNT_SEIZE_CALCULATION_FAILED,
    LIQUIDATE_BORROW_DENOMINATED_COLLATERAL_CALCULATION_FAILED,
    LIQUIDATE_CLOSE_AMOUNT_TOO_HIGH,
    LIQUIDATE_CONTRACT_PAUSED,
    LIQUIDATE_DISCOUNTED_REPAY_TO_EVEN_AMOUNT_CALCULATION_FAILED,
    LIQUIDATE_NEW_BORROW_INDEX_CALCULATION_FAILED_BORROWED_ASSET,
    LIQUIDATE_NEW_BORROW_INDEX_CALCULATION_FAILED_COLLATERAL_ASSET,
    LIQUIDATE_NEW_BORROW_RATE_CALCULATION_FAILED_BORROWED_ASSET,
    LIQUIDATE_NEW_SUPPLY_INDEX_CALCULATION_FAILED_BORROWED_ASSET,
    LIQUIDATE_NEW_SUPPLY_INDEX_CALCULATION_FAILED_COLLATERAL_ASSET,
    LIQUIDATE_NEW_SUPPLY_RATE_CALCULATION_FAILED_BORROWED_ASSET,
    LIQUIDATE_NEW_TOTAL_BORROW_CALCULATION_FAILED_BORROWED_ASSET,
    LIQUIDATE_NEW_TOTAL_CASH_CALCULATION_FAILED_BORROWED_ASSET,
    LIQUIDATE_NEW_TOTAL_SUPPLY_BALANCE_CALCULATION_FAILED_BORROWER_COLLATERAL_ASSET,
    LIQUIDATE_NEW_TOTAL_SUPPLY_BALANCE_CALCULATION_FAILED_LIQUIDATOR_COLLATERAL_ASSET,
    LIQUIDATE_FETCH_ASSET_PRICE_FAILED,
    LIQUIDATE_TRANSFER_IN_FAILED,
    LIQUIDATE_TRANSFER_IN_NOT_POSSIBLE,
    REPAY_BORROW_ACCUMULATED_BALANCE_CALCULATION_FAILED,
    REPAY_BORROW_CONTRACT_PAUSED,
    REPAY_BORROW_NEW_BORROW_INDEX_CALCULATION_FAILED,
    REPAY_BORROW_NEW_BORROW_RATE_CALCULATION_FAILED,
    REPAY_BORROW_NEW_SUPPLY_INDEX_CALCULATION_FAILED,
    REPAY_BORROW_NEW_SUPPLY_RATE_CALCULATION_FAILED,
    REPAY_BORROW_NEW_TOTAL_BALANCE_CALCULATION_FAILED,
    REPAY_BORROW_NEW_TOTAL_BORROW_CALCULATION_FAILED,
    REPAY_BORROW_NEW_TOTAL_CASH_CALCULATION_FAILED,
    REPAY_BORROW_TRANSFER_IN_FAILED,
    REPAY_BORROW_TRANSFER_IN_NOT_POSSIBLE,
    SET_ASSET_PRICE_CHECK_ORACLE,
    SET_MARKET_INTEREST_RATE_MODEL_OWNER_CHECK,
    SET_ORACLE_OWNER_CHECK,
    SET_ORIGINATION_FEE_OWNER_CHECK,
    SET_PAUSED_OWNER_CHECK,
    SET_PENDING_ADMIN_OWNER_CHECK,
    SET_RISK_PARAMETERS_OWNER_CHECK,
    SET_RISK_PARAMETERS_VALIDATION,
    SUPPLY_ACCUMULATED_BALANCE_CALCULATION_FAILED,
    SUPPLY_CONTRACT_PAUSED,
    SUPPLY_MARKET_NOT_SUPPORTED,
    SUPPLY_NEW_BORROW_INDEX_CALCULATION_FAILED,
    SUPPLY_NEW_BORROW_RATE_CALCULATION_FAILED,
    SUPPLY_NEW_SUPPLY_INDEX_CALCULATION_FAILED,
    SUPPLY_NEW_SUPPLY_RATE_CALCULATION_FAILED,
    SUPPLY_NEW_TOTAL_BALANCE_CALCULATION_FAILED,
    SUPPLY_NEW_TOTAL_CASH_CALCULATION_FAILED,
    SUPPLY_NEW_TOTAL_SUPPLY_CALCULATION_FAILED,
    SUPPLY_TRANSFER_IN_FAILED,
    SUPPLY_TRANSFER_IN_NOT_POSSIBLE,
    SUPPORT_MARKET_FETCH_PRICE_FAILED,
    SUPPORT_MARKET_OWNER_CHECK,
    SUPPORT_MARKET_PRICE_CHECK,
    SUSPEND_MARKET_OWNER_CHECK,
    WITHDRAW_ACCOUNT_LIQUIDITY_CALCULATION_FAILED,
    WITHDRAW_ACCOUNT_SHORTFALL_PRESENT,
    WITHDRAW_ACCUMULATED_BALANCE_CALCULATION_FAILED,
    WITHDRAW_AMOUNT_LIQUIDITY_SHORTFALL,
    WITHDRAW_AMOUNT_VALUE_CALCULATION_FAILED,
    WITHDRAW_CAPACITY_CALCULATION_FAILED,
    WITHDRAW_CONTRACT_PAUSED,
    WITHDRAW_NEW_BORROW_INDEX_CALCULATION_FAILED,
    WITHDRAW_NEW_BORROW_RATE_CALCULATION_FAILED,
    WITHDRAW_NEW_SUPPLY_INDEX_CALCULATION_FAILED,
    WITHDRAW_NEW_SUPPLY_RATE_CALCULATION_FAILED,
    WITHDRAW_NEW_TOTAL_BALANCE_CALCULATION_FAILED,
    WITHDRAW_NEW_TOTAL_SUPPLY_CALCULATION_FAILED,
    WITHDRAW_TRANSFER_OUT_FAILED,
    WITHDRAW_TRANSFER_OUT_NOT_POSSIBLE
}
```
