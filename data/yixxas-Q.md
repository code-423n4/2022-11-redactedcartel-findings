## Low-01 - setRewardRecipientPrivileged() can accidentally overwrite reward recipient as it does not check if a reward recipient already exist.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L432-L453

Add the check `if (producerTokens[producerToken].rewardRecipients[lpContract][rewardToken] != address(0)) revert alreadyAdded();` to disallow accidental overwriting of reward recipient that is previously set.

## Low-02 - Unbounded length array in rewardTokens can cause users to be unable to claim rewards

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L396

Each time `rewardTokens` is added, the array increase in size and there is no limit for the number of `rewardTokens` for a `producerToken`. When this happens, user will not be able to `claim()` rewards as the maximum block gas limit can exceed if array gets too large.

Consider adding a limit on number of possible rewardTokens that can be added.