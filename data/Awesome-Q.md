## 1. Event log poisoning in `distributeFees()`

`distributeFees()` is an external function that can be called by anyone who inputs a `token`, but there is no data validation to check if `token` are non-zero. So user can continuously call `distributeFees(`)` with an input of a non-zero value

Affected line of code:
[Line 100-116](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L116)

## 2. Use `delete` to clear variables

rather than using a zero assignment a better way of signifying the intent is to use `delete`.

There are 2 instances where this can be implemented:

[Line 118](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L118), [Line 391](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L391)

```
File: vaults/PxGmxReward.sol
Line 118:    userRewardStates[msg.sender].rewards = 0;

File: src/PirexRewards.sol
Line 391:    p.userStates[user].rewards = 0;
```

Could be refactored to as

```
File: vaults/PxGmxReward.sol
Line 118:    delete userRewardStates[msg.sender].rewards = 0;

File: src/PirexRewards.sol
Line 391:    delete p.userStates[user].rewards = 0;
```

## 3. Absent NatSpec comments

Consider providing NatSpec as it is good for readability, debugging, further development etc.

Consider following the official NatSpec comment guidelines to make the contract more readable.
https://docs.soliditylang.org/en/develop/natspec-format.html

Affected contracts:

[interfaces/IPirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IPirexRewards.sol), [interfaces/IProducer.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IProducer.sol), [interfaces/IAutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IAutoPxGlp.sol), [src/Common.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol)