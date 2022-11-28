## 1. `+=` and `-=` are less efficient

`+=` and `-=` costs around 22 more gas, The amount of gas wasted can be very large when repeated multiple times in a loop.

For example:

File: `PxERC20.sol` [Line 90](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90)

```
            balanceOf[to] += amount;
```

Could be refactored to:

```
            balanceOf[to] = balanceOf[to] + amount;
```

All other of the instances:

File: `PxERC20.sol` [Line 85](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85)

File: `PxERC20.sol` [Line 119](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L119)

File: `PxERC20.sol` [Line 124](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L124)

File: `PxGmxReward.sol` [Line 95](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95)

## 2. Using named returns saves gas

You can save gas by using named return values as a temporary local variable.

For Example:

File: `AutoPxGmx.sol` [Line 164-166](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L164-L166)

```
164:    function totalAssets() public view override returns (uint256) {
165:        return asset.balanceOf(address(this));
166:    }
```

The above could be refactored to the following:

```
164:    function totalAssets() public view override returns (uint256 assetBalance) {
165:        assetBalance = asset.balanceOf(address(this));
166:    }
```

All other instances found:

File: `PxERC20.sol` [Line 83](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L83)

File: `PxERC20.sol` [Line 113](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L113)

File `AutoPxGmx.sol` [Line 177](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L177)

File: `AutoPxGmx.sol` [Line 203](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L203)

## 3. Payable access control functions costs less gas

Functions with access control can be marked as `payable`. This saves about 20 gas on each call by their permissible callers since the compiler doesn't need to check for `msg.value`.

Here are the instances found:

File: `PirexFees.sol` [Line 65](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L65), [Line 85](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L85)

```
65:        onlyOwner

85:        onlyOwner
```

File: `PxERC20.sol` [Line 48](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L48), [Line 65](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L65)

```
48:        onlyRole(MINTER_ROLE)

65:        onlyRole(BURNER_ROLE)
```

File: `AutoPxGmx.sol` [Line 104](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104), [Line 116](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116), [Line 128](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128), [Line 140](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140), [Line 152](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152)

```
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:    function setPlatformFee(uint256 fee) external onlyOwner {

140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:    function setPlatform(address _platform) external onlyOwner {
```
