# [QA-01]

`compound` in AutoPxGmx can be called by anyone and can be sandwiched if a poorly chosen `amountOutMinimum` is used. The idea is to call the function often by adding an incentive to the caller. There is a problematic hidden incentive - the `amountOutMinimum` value is controlled by the caller. This means that MEV bots have incentive to call this function and take a cut of the rewards by setting `amountOutMinimum` to a lower value than could be received by the vault.

The function argument `amountOutMinimum` is [on line 244](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L244). It is used in the Uniswap swap [on line 275](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L275). It can be set to any value except zero, enabling the majority of the value from the swap to be lost.

## Recommendation

Disincentive sandwiching of this swap. There should be additional checks that the `amountOutMinimum` function argument is a reasonable value. One way to do this is using a TWAP or oracle to get the exchange rate and use `gmxBaseRewardAmountIn` to calculate what the minimum allowable `amountOutMinimum` is.

# [QA-02]

`_setupRole` is deprecated. Instead use `_grantRole`. This also removes a function call because `_setupRole` calls `_grantRole`. `_setupRole` is found [in PxERC20](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L37)

OpenZeppelin [clearly indicates](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/8f8fd84f1e60426a5e785d6b5b2524938271bb05/contracts/access/AccessControl.sol#L203) this function is deprecated

## Recommendation

Use `_grantRole` not `_setupRole`

# [QA-03]

[This comment about approving the Uniswap router](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L86) is wrong. It was accidentally copied [from another file](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L95).
```
// Approve the Uniswap V3 router to manage our base reward (inbound swap token)
```

## Recommendation
Change the comment to `Approve platform address to manage our base reward`

# [QA-04]

`_platform` is already of type address in this line. It does not need to be cast to address
```
gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
```

## Recommendation
```diff
- gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
+ gmxBaseReward.safeApprove(_platform, type(uint256).max);
```

# [QA-05]

Identical functions in AutoPxGlp and AutoPxGmx have slightly different comments.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L170-L171
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L148-L149

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L196-L197
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L174-L175
```diff
-         @param  assets  uint256  Assets
-         @return uint256  Shares
+         @param  assets   uint256  Assets amount
+         @return          uint256  Shares amount
```

## Recommendation

Use the same comments in different contracts of a project when the code is the same.
