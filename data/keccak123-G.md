# [G-01] Replace require with errors

Solidity errors [save gas compared to require](https://blog.soliditylang.org/2021/04/21/custom-errors/)

Three lines use require:
1. https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L68
2. https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L137
3. https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L355

## Recommendation

Use solidity errors to replace require for gas savings

# [G-02] Change constant variables to private

Variables that are private or internal use less gas than public or external variables

Many variables can make use of this change if the contract does not need to make these variables public.
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L18-L22
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L18
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L20-L23
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L9-L10
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L36-L37
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L50-L52
- https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L55-L56

## Recommendation

Make variables private or internal if they do not need to be public or external

# [G-03] Make certain functions payable

A payable function saves gas compared to one that is not payable. If a function has an onlyOwner modifier or has similar access controls to prevent the function from receiving ETH, the function can be payable to save gas.

Many functions have the onlyOwner modifier and can be more efficient
- setWithdrawalPenalty
- setPlatformFee
- setCompoundIncentive
- setPlatform
- setPoolFee
- setWithdrawalPenalty
- setPlatformFee
- setCompoundIncentive
- setPlatform

## Recommendation

Make all functions with onlyOwner payable

# [G-04] Unchecked saves gas

Use unchecked when possible for gas savings.
Line 403: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L403
```diff
- uint256 amount = (rewardState * userRewards) / globalRewards;
+ unchecked { uint256 amount = (rewardState * userRewards) / globalRewards; }
```

## Recommendation

Use unchecked for gas savings when there is no overflow or underflow risk