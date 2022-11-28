### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 3*

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
163: for (uint256 i; i < len; ++i) {
351: for (uint256 i; i < pLen; ++i) {
396: for (uint256 i; i < rLen; ++i) {
```

### G-02 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 6*

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
361: producerState.rewardStates[rewardTokens[i]] += r;
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol

```
85: balanceOf[msg.sender] -= amount;
90: balanceOf[to] += amount;
119: balanceOf[from] -= amount;
124: balanceOf[to] += amount;
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol

```
95: rewardState += rewardAmount;
```


### G-03 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 1*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L207-L221

```
function getUserState(ERC20 producerToken, address user)
        external
        view
        returns (
            uint256 lastUpdate,
            uint256 lastBalance,
            uint256 rewards
        )
```
