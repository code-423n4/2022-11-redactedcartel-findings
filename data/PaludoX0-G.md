### Usage of uints/ints smaller than 32bytes (256bits) incurs overhead
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L28
    uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;  
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L26
    uint24 public poolFee = 3000;

Usage of uints/ints smaller than 32bytes (256bits) incurs overhead and gas usage may be higher. 
This is because the EVM operates on 32 bytes at a time. Use a larger size then downcast where needed

For example, these are the results by changing **AutoPxGmx.sol/#L26** from **uint24** to **uint256** and running function testCompound in **AutoPxGmx.t.sol**

>| Function Name                               | min             | avg    | median | max    | # calls |
>| compound                                    | 468452          | 468452 | 468452 | 468452 | 1       |
>
>| Function Name                               | min             | avg    | median | max    | # calls |
>| compound                                    | 468343          | 468343 | 468343 | 468343 | 1       |

### Assert Reverts the Calls without returning gas
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225
        `assert(feeAmount + postFeeAmount == assets); `
Assert reverts call and doesn't return any gas. It used mostly to catch errors during development, it could be changed with require or if/revert/error statement.

### Save a function call
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L338 L380 and L423
You can use `compound(1,1,true)` instead of using `beforeDeposit(address(0), 0, 0)` to spare a function call and therefore saving gas

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L379
You can use directly compound `(poolFee, 1, 0, true)` instead of using beforeDeposit(address(0), 0, 0) to spare a function call and therefore saving gas

### CACHE (totalAssets() - assetsBeforeClaim)
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L288 and 290
`totalAssets()` corresponds to  `asset.balanceOf(address(this))` as per L163
Since this operation is run at line 288 and 290 you can cache it (as already done in AutoPxGlp#L250)