
## G-01 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 6 instances of this issue_

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
File: src/PirexRewards.sol

361: producerState.rewardStates[rewardTokens[i]] += r;
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol

```
File: src/PxERC20.sol

85: balanceOf[msg.sender] -= amount;
90: balanceOf[to] += amount;
119: balanceOf[from] -= amount;
125: balanceOf[to] += amount;
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol

```
File: src/PxGmx.sol

95: rewardState += rewardAmount;
```

--------

## G-02 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
File: src/PirexRewards.sol

163: for (uint256 i; i < len; ++i) {
351: for (uint256 i; i < pLen; ++i) {
396: for (uint256 i; i < rLen; ++i) {
```

----------------

## G-03 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
File: src/PirexRewards.sol

207: function getUserState(ERC20 producerToken, address user)
```

------

## G-04 EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol

```
File: src/PxERC20.sol

9: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
10: bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```

----------------