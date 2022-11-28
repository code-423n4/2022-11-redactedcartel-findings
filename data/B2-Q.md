## Missing `zero address` check in `constructor`

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
        PxERC20(_pirexRewards, "Pirex GMX", "pxGMX", 18)
```

>File: /src/PxGmx.sol [(line 10)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L10)

## `require()` should be used instead of `assert()`

```
        assert(feeAmount + postFeeAmount == assets);
```

>File: src/PirexGmx.sol [(line 225)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225)

## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
          contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
```
>File: src/vaults/AutoPxGmx.sol [(line 14)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L14)

```
          contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
```
>File: src/vaults/AutoPxGlp.sol [(line 14)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L14)

```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L20

## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers, locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
            (block.timestamp - globalState.lastUpdate) *
```
>File: src/vaults/PxGmxReward.sol (line 54)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L54)
```
        globalState.lastUpdate = block.timestamp.safeCastTo32();
```
>File: src/vaults/PxGmxReward.sol (line 54)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L57)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L61
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L77
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L79
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L291
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L293
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L297
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L314
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L316
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L319
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L325
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L83

## `INITIALIZE` functions can be front run

This function can be called by everyone. So an attacker may watch yhe mempool for this  function and may frontrun it by supplying more gas.
This may result in unintended behaviour

```
      function initialize() public initializer {
```
>File: src/PirexRewards.sol [(line 85)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L85)

## Event is missing `indexed` fields

Each event should use three indexed fields if there are three or more fields.

```
    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
    event DistributeFees(
        ERC20 indexed token,
        uint256 distribution,
        uint256 treasuryDistribution,
        uint256 contributorsDistribution
```
>File: src/PirexFees.sol [(line 36-40)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L36-L40)
```
    event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);

```
>File: src/vaults/PxGmxReward.sol [(line 21)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L22-L27
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L29-L32
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L44-L53
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L39-L48
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L50-L54
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L56-L61
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L63-L66
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L97-LL102
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L104-L112
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L115-L123
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L125-L131
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L133-L138
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L27-L31

## `NatSpec` is incomplete

```
   /// @audit Missing:  `@return`
    /**
        @notice Override transfer method to allow for pre-transfer internal hook
        @param  to      address  Account receiving apxGLP
        @param  amount  uint256  Amount of apxGLP
    */
    function transfer(address to, uint256 amount)
```
> File: src/vaults/PirexERC4626.sol [(line 233-238)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L233-L238)

```
   /// @audit Missing: '@param`
    /**
        @notice Override the withdrawal method to make sure compound is called before withdrawing
     */
    function withdraw(

```
> File: src/vaults/AutoPxGlp.sol [(line 433-436)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L433-L436)
```
	Other instances of this issue are:
```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L446-L449
   /// @param Missing: `@param` & `@return`
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L459-L462
   /// @param Missing: `@param` 
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L250-L256
   /// @param Missing: `@return` 

## File is missing `NatSpec`

```
// SPDX-License-Identifier: MIT
```
> File: src/interfaces/IAutoPxGlp.sol [(line 0)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IAutoPxGlp.sol)

```
// SPDX-License-Identifier: MIT
```
> File: src/interfaces/IProducer.sol [(line 0)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IProducer.sol)

```
// SPDX-License-Identifier: MIT
```
> File: src/interfaces/IPirexRewards.sol [(line 0)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IPirexRewards.sol)

## Set `garbage` value in `mapping` for deleting that

If there is a mapping data structure present inside struct, then deleting the struct doesn't delete the mapping. Instead one should use lock to lock that data structure from further use.


```
        delete producerTokens[producerToken].rewardRecipients[msg.sender][
```
>File: src/PirexRewards.sol [(line 139)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L139)
```
        delete producerTokens[producerToken].rewardRecipients[lpContract][
```
>File: src/PirexRewards.sol [(line 470)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L470)

## Inconsistent solidity pragma 

The source files have different solidity compiler ranges referenced. This leads to potential security flaws between deployed contracts depending on the compiler version chosen for any particular file. It also greatly increases the cost of maintenance as different compiler versions have different semantics and behavior.

> File: src/interfaces/IPirexRewards.sol [(line 2)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L2) uses `pragma solidity >=0.8.0;` whereas rest of the contracts uses `pragma solidity 0.8.17;`

