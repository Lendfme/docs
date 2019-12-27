## How to perform liquidation on lendf.me

### About Lendf.Me

The Protocol for Decentralized Lending Market, you can find more about us at [here](https://docs.lendf.me/faq)

Our github repo is [lendfMe](https://github.com/Lendfme)

### Contract Address

__MainNet:__

```
moneyMarket: 0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea
liquidator : 0x932906251479106D96904184aA4985C1a291B35d
```

__Rinkeby:__

```
moneyMarket: 0x2FD380b99E0c6ff16F569FB8214b40509F776764
liquidator : 0x8E8627FC8e2359fDc9492540D646c3F4d979A44A
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

_**Notice:The asset that user wants to liquidate must approve to the `Liquidator.sol` contract at first.**_

You can get `requestedAmountClose` by visiting our public API with variables `targetAccount`, `assetBorrow`, `assetCollateral`, such as: [https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549](https://api.lendf.me/v1/liquidate?targetAccount=0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea&assetBorrow=0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2&assetCollateral=0xeb269732ab75A6fD61Ea60b06fE994cD32a83549)

you will get data like below:

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

_Notice: You can use below equation to calculate liquidation quantity roughly:_

```
liquidateAmountWETH = Math.abs(shortfall_weth*10**18 / (10**18 + liquidationDiscount - collateralRatio))
requestAmount = min(borrowAmount, collateralAmount, Math.floor(liquidateAmountWETH * 10**36 / assetBorrowPrice))
```
_You can get liquidationDiscount，collateralRatio，assetBorrowPrice from a public API：[https://api.lendf.me/v1/info?data=markets](https://api.lendf.me/v1/info?data=markets)_


- 1) You can copy the contract [Liquidator.sol](https://github.com/Lendfme/front_end_sc/blob/master/contracts/Liquidator.sol) and open it on the [Remix](https://remix.ethereum.org), initialize the contract by the contract address`0x932906251479106D96904184aA4985C1a291B35d(mainnet)`, and then call the function which is mentioned above.

- 2) You can use the popular JavaScript library [web3](https://github.com/ethereum/web3.js/) to call the contract function directly, you can also choose other scripting language.

- 3) You can visit [https://monitor.lendf.me](https://monitor.lendf.me), then liquidate on the website, but you need to install extension MetaMask at first.

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
