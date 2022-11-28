## 1. Event log poisoning in `distributeFees()`

A user can call the `distributeFees()` function with no amount of token to emit a `DistributeFees()` event. Because of this, users can spam the contract with useless events.

`PirexFees.sol` [Line 100 - Line 116](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L116)

```solidity
100:    function distributeFees(ERC20 token) external {
101:        uint256 distribution = token.balanceOf(address(this));
102:        uint256 treasuryDistribution = (distribution * treasuryFeePercent) /
103:            FEE_PERCENT_DENOMINATOR;
104:        uint256 contributorsDistribution = distribution - treasuryDistribution;
105:
106:        emit DistributeFees(
107:            token,
108:            distribution,
109:            treasuryDistribution,
110:            contributorsDistribution
111:        );
112:
113:        // Favoring push over pull to reduce accounting complexity for different tokens
114:        token.safeTransfer(treasury, treasuryDistribution);
115:        token.safeTransfer(contributors, contributorsDistribution);
116:    }
```

To prevent this issue, consider requiring that the variable `distribution` at Line 101 is not zero.

## 2. Use `delete` to clear variables

`delete` assigns the default value back to the given variable. For example, executing a delete to a `uint` variable will set the variable's value to 0.

The reason to use `delete` is because it better conveys the intention of what you are trying to do.

Here are the instances:

`PxGmxReward.sol` [Line 118](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L118)

`PirexRewards.sol` [Line 391](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L391)

## 3. Empty Event

The event below has no parameters in it to emit anything. Consider either refactoring or removing this event.

File: PirexGmx.sol [Line 144](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```solidity
144:    event ClearVoteDelegate();
```

## 4. Insufficient NatSpec

It is recommended that all functions and state variables should be well commented using the [NatSpec](https://docs.soliditylang.org/en/develop/natspec-format.html) documentation.

Here some contracts that are missing NatSpec entirety:

[File: interfaces/IPirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IPirexRewards.sol)

[File: interfaces/IProducer.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IProducer.sol)

[File: interfaces/IAutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IAutoPxGlp.sol)

[File: interfaces/Common.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol)

## 5. Typo errors

`DelegateRegistry.sol` [Line 10](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/external/DelegateRegistry.sol#L10)

Change "indeces" -> "indexes"

## 6. Variable names are too short

Very short identifier names e.g. (e, x, \_t) can make code harder to read and potentially less maintainable.

For example:

`File: PxGmxReward.sol`: [Line 71](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
71:        UserState storage u = userRewardStates[user];
```

The variable name `u` is not very descriptive

## 7. Unnecessarily emitting `block.timestamp`

Events emitted by Solidity already include the timestamp and block number. Emitting it twice only increases the gas used for the transaction.

Here are the instances of this issue:

`File: PxGmxReward.sol` ([Line 61](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L61), [Line 83](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L83))

```
61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);
...
83:        emit UserAccrue(user, block.timestamp, balance, rewards);
```

`File: PirexRewards.sol` [Line 297](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L297), [Line 323-328](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L323-L328)

```
297:        emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);
...
323:            emit GlobalAccrue(
324:                producerToken,
325:                block.timestamp,
326:                totalSupply,
327:                rewards
328:            );
```
