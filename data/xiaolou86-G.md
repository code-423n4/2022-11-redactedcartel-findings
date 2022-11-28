1. ++i should be unchecked{++i} when it is not possible for them to overflow.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396

2. <x> += <y> costs more gas than <x> = <x> + <y>.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L361
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L124
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95
