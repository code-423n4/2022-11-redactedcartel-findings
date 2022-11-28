### Avoid storage of uints or ints smaller than 32 bytes, if possible
Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed
___
[AutoPxGmx.sol: L26](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L26)
```solidity
    uint24 public poolFee = 3000;
```
___
[AutoPxGmx.sol: L46](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L46)
```solidity
        uint24 fee,
```
___
[AutoPxGmx.sol: L104](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L104)
```solidity
    function setPoolFee(uint24 _poolFee) external onlyOwner {
```
___
[AutoPxGmx.sol: L243](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L243)
```solidity
        uint24 fee,
```
___
___


### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}` instead, as shown below:

[PirexRewards.sol: L163-167](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L163-L167)
```solidity
        for (uint256 i; i < len; ++i) {
            if (address(rewardTokens[i]) == address(rewardToken)) {
                revert TokenAlreadyAdded();
            }
        }
```
Suggestion:
```solidity
        for (uint256 i; i < len;) {
            if (address(rewardTokens[i]) == address(rewardToken)) {
                revert TokenAlreadyAdded();

            unchecked {
              ++i;
          }
        }
```
___
Similarly, for the following `for` loops:

[PirexRewards.sol: L351](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L351)

[PirexRewards.sol: L396](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L396)
___
___

