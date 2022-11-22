#### M1. Highly Permissive Role Access
The owner of the `RewardTracker` contract can withdraw any ERC20 token from the contract, that is not in `isDepositToken` mapping. The owner can add and remove tokens from mapping at any time. 
As a result, owner could exclude a specific token from `isDepositToken` mapping, withdraw all user deposits from the contract, and add this token back to `isDepositToken` mapping.  As a result - users’ funds are not properly protected.
It’s recommended to withdraw only exceeded tokens from the contract, for example:
Users deposited 1000 XYZ tokens into the contract.
Someone sent by mistake 200 XYZ tokens to the contract.
An owner should be able to withdraw only `totalTokensBalance(1200) - totalTokenDeposits(1000)` = 200 XYZ tokens, not to affect user’s balances.

**Path:** ./external/RewardTracker.sol : withdrawToken()
**Recommendation:** Allow to withdraw only exceeded tokens in the contract, not to affect user’s balances.

#### L1. Missing Zero Address Validation
During initialization, there is no address default (zero) value check. The reported fields are not possible to update after deployment, so such a mistake will lead to contract redeployment.

**Path:** ./external/RewardTracker.sol : initialize(),
./external/vaults/PirexERC4626 : constructor()
**Recommendation:** Add a zero check for the `_distributor`, `asset` parameters.