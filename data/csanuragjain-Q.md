## Burn function does nothing

Contract:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L19

Issue:
1. The burn function is overridden function from PxERC20 but does not actually do anything. 
2. This means if any contract in future uses burn from PxGmx then it will do nothing but the caller will believe that burn was success

Recommendation:
Implement Burn function to actually burn tokens