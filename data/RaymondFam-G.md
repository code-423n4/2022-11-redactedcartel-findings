## `uint256` Can be Cheaper Than `uint8` and Other Unsigned Integer Type of Smaller Bit Size
When dealing with function arguments or memory values, there is no inherent benefit because the compiler does not pack these values. Your contract’s gas usage may be higher because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. The EVM needs to properly enforce the limits of this smaller type.

It is only more efficient when you can pack variables of uint8, uint16, uint32, uint64, ... into the same storage slot with other neighboring variables smaller than 32 bytes. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);

83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)
```
## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
65:        onlyOwner

85:        onlyOwner
```
[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
48:        onlyRole(MINTER_ROLE)

65:        onlyRole(BURNER_ROLE)
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:    function setPlatformFee(uint256 fee) external onlyOwner {

140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:    function setPlatform(address _platform) external onlyOwner {
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

As an example, the following `+=` instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90

```
            balanceOf[to] = balanceOf[to] + amount;
```

All other instances entailed:

[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
124:            balanceOf[to] += amount;
```
[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
95:            rewardState += rewardAmount;
```
Similarly, as an example, the following `-=` instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85

```
        balanceOf[msg.sender] = balanceOf[msg.sender] - amount;
```
All other instances entailed:

[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
119:        balanceOf[from] -= amount;
```
## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

As an example, the following instance of code block can refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80-L100

```
    function transfer(address to, uint256 amount)
        public
        override
        returns (bool status)
    {
        ...
        status = true;
    }
```
All other instances entailed:

[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
113:    ) public override returns (bool) {
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
164:    function totalAssets() public view override returns (uint256) {

177:        returns (uint256)

203:        returns (uint256)
```
## `||` Costs Less Gas Than Its Equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

For instance, the `if conditional` instance below could be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L115

```
        if (!(globalRewards == 0 || userRewards == 0)) {
```