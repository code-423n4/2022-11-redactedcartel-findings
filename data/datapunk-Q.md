## L1. Depending on how many `rewardTokens`, the owner of `PirexRewards` will add to `producerTokens[producerToken].rewardTokens`, looping through the array may result in OOG.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L163

## L2. Avoid event spamming from `userAccrue` in `PirexRewards.sol`
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L281
`userAccrue` generates `UserAccrue` event, regardless whether or not there are any changes. 
bracketing the updating logic and emit inside of the following condition 
```
 if (block.timestamp != u.lastUpdate || balance != u.lastBalance) {
     ...
 }
 ```
 
 Similar issues exist in `PxGmxReward.sol` for `_globalAccue` and `_userAccrue`
 https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L49
 https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L68

## L3. event `ClaimRewards` may emit rewards values that are inconsistent with the actual rewards.
This is due to the recalculation in place for `rewardAmounts`.
`ClaimRewards` should emit `rewardAmounts` values in place the gmxBaseRewards, etc.

## L4. `PxGmxReward` is not inheriting from ERC20, yet it is accessing ERC20 functions. 
`AutoPxGlp` inherits from both `PxGmxReward` and `PirexERC4626`, which in turn from `ERC20`. Therefore

## L5. `withdraw` and `redeem` functions in `AutoPxGmx` can reuse the functions defined in `PirexERC4626` by calling `PirexERC4626.withdraw` and `PirexERC4626.redeem`


## L6. solidity always rounds down
Fees can be calculated more accurately with rounding up as needed. For example,

`feeAmount = (assets * fees[f]) / FEE_DENOMINATOR; `
can be changed to 
`feeAmount = (assets * fees[f] + FEE_DENOMINATOR / 2) / FEE_DENOMINATOR; `
