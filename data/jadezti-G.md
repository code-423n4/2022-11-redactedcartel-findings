
### [GAS-1] Use `unchecked` arithmetic operation when no `overflow/underflow` possible

_Instances (2)_

[Link to code](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L126)
```
File: src/vaults/PxGmxReward.sol

126:   rewardState = _rewardState - amount;
```
Line 117 & 122 guarantees `amount` <= `_rewardState` 

[Link to code](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)
```
File: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol

104:  uint256 contributorsDistribution = distribution - treasuryDistribution;
```
Line 20, 23, 88-89, and 102-103 guarantees `treasuryDistribution <= distribution`


### [GAS-2]  `X = X + Y` is more gas efficient than `X += Y` for state variables

_Instances (1)_

[Link to code](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95)
```
File:     src/vaults/PxGmxReward.sol

95:          rewardState += rewardAmount;
```
