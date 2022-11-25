# Redacted Cartel Gas Optimization Findings
## Summary
The Gas savings are calculated using the `scripts/forgeTest.sh --gas-report` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00 | Claiming rewards from `PirexRewards` requires redundant calls to `harvest` function | PirexRewards.sol | 1 | not calculated | not calculated |
| G-01 | Superfluous `assert` statement | PirexGmx.sol | 1 | 9216 | 95 |

## [G-00] Claiming rewards from `PirexRewards` requires redundant calls to `harvest` function
A user can claim rewards from the `PirexRewards` contract with the `claim` function ([https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L373](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L373)).  

The `claim` function calls the `harvest` function which claims rewards from the `producer` for multiple `producerToken`s ([https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L346-L347](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L346-L347)).  

So when a user wants to `claim` rewards from the `PirexRewards` contract for multiple `producerToken`s, he needs to call the `claim` function once for each `producerToken` which results in multiple of the same calls to `producer.claimRewards`.  

One call to `producer.claimRewards` is enough.  

This can be achieved by implementing a `claimBatch` function in `PirexRewards` that calls `harvest` and thereby `producer.claimRewards` once and then iterates over all provided `producerToken`s.  

This results in substantial Gas savings since one call to `producer.claimRewards` requires on average `161614` Gas.  

## [G-01] Superfluous `assert` statement
[https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225)  

The `assert` statement is redundant since it will always be true.  

This is because the line above it calculates:  
```solidity
postFeeAmount = assets - feeAmount;
```

By applying basic algebra it follows that:  
```solidity
assets = postFeeAmount + feeAmount
```
This is exactly what the `assert` checks. But as you can see the check will always be true.  

So the `assert` statement can just be removed.  

Deployment Gas saved: **9216**  
Method call Gas saved: **95**  