**[[1]]** When using `safeApprove()`, it's better to first set approve amount to zero and then set the new value, because if for any reason some spending dust has been left for that address then code would revert and contract would stuck in broken state. because contract assumes that other contract would spend all the allowance and make allowance zero again, but due to upgrades or .... other contract may not behave in this manner, so it's better to write the code in some way that the logic never breaks.
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L503-L507
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L346-L347
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L391

**[[2]]** In function `removeRewardToken()` of `PirexRewards` contract, code don't check that `removalIndex` is less than array `length`, so if someone send incorrect values code would revert with wrong messages. for example if `producerTokens[producerToken].rewardTokens` was empty then code would revert with underflow error in the line `uint256 lastIndex = rewardTokens.length - 1` and if `removalIndex >= rewardTokens.length` then code would revert with out of bound error message.
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L174-L197

**[[3]]** Function `getRewardRecipient()` of `PirexRewards` contract, should return `user` as a recipient when user didn't set any recipient but instead it returns zero address. when the user didn't set any recipient then the value of `producerTokens[producerToken].rewardRecipients[user][rewardToken]` is `0x0` and function just returns this value which don't make sense because `0x0` address is not the real recipient, instead function could check for the value and if it's `0x0` it should return `user` address. like this:
```
    function getRewardRecipient(
        address user,
        ERC20 producerToken,
        ERC20 rewardToken
    ) external view returns (address) {
        address result = producerTokens[producerToken].rewardRecipients[user][rewardToken];
        if(result == address(0))
                     result  = user;
        return result;
    }
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L250-L264

**[[4]]** library `SafeTransferLib` imported two times in `AutoPxGlp` contract.
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L6-L8