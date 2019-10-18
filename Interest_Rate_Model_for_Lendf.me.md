# Interest Rate Model for Lendf.me

Interest rates for lending and borrowing on LENDF.ME are set algorithmically based on supply and demand. 

As demand increases, the interest rates go up. As supply increases, the interest rates go down.
The equations that we use on LENDF.ME for interest rate are similar as the following,

### *Borrow Interest Rate = basis + Utilization Rate * 20%*

### *Utilization Rate = total borrows / total supplies*

*in practical, basis could be different from assets

Lending interest rate is calculated based on the borrowing interest rate, the equation is as follows:

### *Lending Interest Rate = Borrow Interest Rate * Utilization Rate * 0.95*

*5% of the lending rate is sent backing to lendf.me asset pool.*

You can retrieve real time interest rate of supplying and borrowing by call moneymarket contract function, named “markets()”, here is the description,

*markets(address) returns (bool, uint, address, uint, uint, uint, uint, uint, uint)*

- **input:**

  *address asset: address of asset*

- **output**

  *bool isSupported: if asset is activated in current market;*
  
  *uint blockNumber: current block number of Ethereum;*
  
  *address interestRateModel: interest mode address of asset;*
  
  *uint totalSupply: total amount of asset supplied;*
  
  *uint supplyRateMantissa: supply interest rate in one block cycle.*
  
  *uint supplyIndex: index of supply interest;*
  
  *uint totalBorrows: total amount of asset borrowed;*
  
  *uint borrowRateMantissa: borrowinterest rate in one block cycle.*
  
  *uint borrowIndex: index of borrow interest;*



The contract in Mainnet is: *[0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea](https://etherscan.io/address/0x0eEe3E3828A45f7601D5F54bF49bB01d1A9dF5ea)*

Have fun!
