1. Unused Custom Error

This custom erorr of [AlreadySet()](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L60) was'nt ever used on the contract. So it can be deleted or used it instead 

2. Fn removeRewardToken() need address check validation

Since another fn was used to check, it better to check the zero address of `rewardToken`

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L179

```
if (address(rewardToken) == address(0)) revert ZeroAddress();
```

3. Missing Indexed 

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L33 

```
event SetProducer(address indexed producer);
```