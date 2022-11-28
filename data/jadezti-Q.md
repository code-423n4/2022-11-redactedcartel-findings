
## [NC-1]  A fixed version of solidity compiler is preferred

[Link to code](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L2)
```
File: src/vaults/PirexERC4626.sol
2: pragma solidity >=0.8.0;
```
To replace `pragma solidity >=0.8.0;` with `pragma solidity 0.8.17;` so as to be consistent with other smart contract files.


## [NC-2] Comment messages inconsistent with code

_Instances (2)_

_Instance 1_:
```
File:  src/PirexRewards.sol
336:   @return rewardAmounts ERC20[] Reward token amounts

343:   uint256[] memory rewardAmounts
```
The above [Line 336](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L336) should be:  `@return rewardAmounts int256[] Reward token amounts`, with reference to [Line 343](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L343).

_Instance 2_:
```
File:  src/vaults/AutoPxGlp.sol

86:    // Approve the Uniswap V3 router to manage our base reward (inbound swap token)
87:    gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
```
The above [Line 86](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L86)  `Approve the Uniswap V3 router to manage our base reward (inbound swap token)` should be `Approve the platform (e.g. PirexGmx) to manage our base reward (inbound swap token)`


## [NC-3] State variable `owner` is shadowed by function parameter

_Instances (6)_

Contract `AutoPxGmx` and `AutoPxGlp` are `owned` contracts which inherit the state variable `owner`. However, several functions of the contracts shadow the state variable `owner`. This may confuse users. The suggestion is to change the function parameter to `address _owner` and make correspoding changes in the function body.

_Instance 1_:
File:  src/vaults/AutoPxGmx.sol
[Line 342](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L342):     address owner

_Instance 2_:
File: src/vaults/AutoPxGmx.sol
[Line 318](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L318):     address owner

_Instance 3_:
File: src/vaults/AutoPxGlp.sol
[Line 439](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L439):     address owner

_Instance 4_:
File: src/vaults/AutoPxGlp.sol
[Line 452](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L452):      address owner

_Instance 5_:
File: src/vaults/AutoPxGlp.sol
[Line 488](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L488):     address owner

_Instance 6_:
File: src/vaults/AutoPxGlp.sol
[Line 502](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L502):     address owner


## [NC-4] `AutoPxGmx.compound()` function should be refactored
Function [`setPoolFee()`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104-L110) set the state variable `poolFee`.  So, function `AutoPxGmx.compound()` can use the `poolFee` directly and remove the `fee` parameter from its input parameters. Removing `fee` parameter also (1) reduces the risk of setting it to an inappropriate value by a user when calling the `compound()` function, (2) saves gas.

Changes to make:
  1 Remove [Line 243](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L243): `uint24 fee,`
  2 Replace [Line 272](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L272): `fee: fee,` with `fee: poolFee,`

Other relevant changes: Replace [Line 321](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L321), [Line 345](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L345), and [Line 227](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L227) with `compound(1, 0, true);`
