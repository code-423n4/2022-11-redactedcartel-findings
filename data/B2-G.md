## Unused named `returns`

Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity:

```
        return (userState.lastUpdate, userState.lastBalance, userState.rewards);
```
>File: src/PirexRewards.sol [(line 220)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L220)

##  Multiple `address` mappings can be combined into a single mapping of `address` to a struct, where appropriate.

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

```
       22        mapping(address => UserState) userStates;
       24        mapping(address => mapping(ERC20 => address)) rewardRecipients;

```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L22 with 
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L24


```
       23            mapping(ERC20 => uint256) rewardStates;
       31            mapping(ERC20 => ProducerToken) public producerTokens;

```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L23 with 
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L31

## `internal` functions only called once can be `inlined` to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
    function _globalAccrue() internal {
```
>File: src/vaults/PxGmxReward.sol [(line 49)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L49)

```
    function _userAccrue(address user) internal {
```
>File: src/vaults/PxGmxReward.sol [(line 68)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L68)
```
       Other instances of this issue are
```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L222
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L474

## `<x> += <y>` costs more gas than <x> = `<x> + <y>` for state variables

```
            rewardState += rewardAmount;
```
>File: src/vaults/PxGmxReward.sol [(line 95)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95) 

## Don't emit block.timestamp

```
        emit GlobalAccrue(block.timestamp, totalSupply, rewards);
```
>File: src/vaults/PxGmxReward.sol [(line 61)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L61)
```
        emit UserAccrue(user, block.timestamp, balance, rewards);
```
>File: src/vaults/PxGmxReward.sol [(line 83)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L83)

## FUNCTIONS GUARANTEED TO `REVERT` WHEN CALLED BY `NORMAL` USERS CAN BE MARKED `PAYABLE`

If a function modifier such as onlyOwneris used, the function will revert if a normal user tries to pay the function. Marking the function as payablewill lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```
    function setVoteDelegate(address voteDelegate) external onlyOwner {
```
>File: src/PirexGmx.sol [(line 884)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L884)


```
    function initiateMigration(address newContract)
```
>File: src/PirexGmx.sol [(line 921)](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L921)
```
       Other instances of this issue are
```
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L272
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L313
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L862
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L909
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L956
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L151
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L179
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L432
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L461
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L63
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128
* https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140