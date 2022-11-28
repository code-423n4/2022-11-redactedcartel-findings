1. There is double SafeTransferLib import in AutoPxGlp
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L6-L8

2. `AutoPxGlp::setPlatform` should be checked for being a contract and ideally if supports specific interface (EIP165). This contract is crucial to AutoPxGlp, so additional care should be put into making sure taht changing it won't break anything.
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130

3. `PxGmxReward` refers to a function that it does not implement
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L72
PxGmxReward contract is not an ERC20, nor does it implement `balanceOf` function. 
Because of that lastBalance always will be 0, hence userRewardStates[user].rewards will always be zero, 
which leads to user loosing all the funds deposited to this contract. Of course at the moment only AutoPxGlp uses this 
contract, and it does implement ERC20, but it can lead to serious problems when used by itself/inherited from
some other contract.

```solidity
72: uint256 balance = ERC20(address(this)).balanceOf(user);
```

Mitigation:
It should be at least clearly stated in the documentation that this contract is not meant to be used by itself, or possibly checked if class that inherits from it supportsInterface ERC20 (ERC165), or PxGmxReward should implement ERC20 itself