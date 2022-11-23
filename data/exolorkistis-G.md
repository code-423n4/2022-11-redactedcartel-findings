[1]  USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 14 instances of this issue*

```
File: 2022-11-redactedcartel/src/Common.sol

5:  uint32 lastUpdate;

6:  uint224 lastSupply;

11:  uint32 lastUpdate;

12:  uint224 lastBalance;
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol#L5-L6](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol#L5-L6)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol#L11-L12](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol#L11-L12)

```
File:  2022-11-redactedcartel/src/PirexFees.sol

20:  uint8 public constant FEE_PERCENT_DENOMINATOR = 100;

23:   uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

28:   uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;

35:   event SetTreasuryFeePercent(uint8 _treasuryFeePercent);

83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)

```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L35](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L35)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83)

```
File:  2022-11-redactedcartel/src/PxERC20.sol

28:  uint8 _decimals

```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L28](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L28)

```
File:  2022-11-redactedcartel/src/vaults/AutoPxGmx.sol

46:  uint24 fee,

48:   uint160 sqrtPriceLimitX96,

243:  uint24 fee,

245:  uint160 sqrtPriceLimitX96,
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L46](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L46)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L48](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L48)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L243](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L243)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L245](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L245)


[2] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There are 17 instances of this issue:*

```
File:  2022-11-redactedcartel/src/PirexFees.sol

20: uint8 public constant FEE_PERCENT_DENOMINATOR = 100;

23:  uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

28:  uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28)

```
File: 2022-11-redactedcartel/src/PirexGmx.sol

44: uint256 public constant FEE_DENOMINATOR = 1_000_000;

47: uint256 public constant FEE_MAX = 200_000;
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L47](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L47)

```
File:  2022-11-redactedcartel/src/PxERC20.sol

9:  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

10:  bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9-L10](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9-L10)

```
File: 2022-11-redactedcartel/src/vaults/AutoPxGlp.sol

18: uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;

19:  uint256 public constant MAX_PLATFORM_FEE = 2000;

20:   uint256 public constant FEE_DENOMINATOR = 10000;

21:   uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

22:   uint256 public constant EXPANDED_DECIMALS = 1e30;
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L18-L22](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L18-L22)

```
File: 2022-11-redactedcartel/src/vaults/AutoPxGmx.sol

18-19:  IV3SwapRouter public constant SWAP_ROUTER =
        IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45);

20: uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;

21:  uint256 public constant MAX_PLATFORM_FEE = 2000;

22:   uint256 public constant FEE_DENOMINATOR = 10000;

23:   uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L18-L23](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L18-L23)

[3]  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 11 instances of this issue:*

```
File:  2022-11-redactedcartel/src/PirexGmx.sol

739-746:  function claimRewards()
        external
        onlyPirexRewards
        returns (
            ERC20[] memory producerTokens,
            ERC20[] memory rewardTokens,
            uint256[] memory rewardAmounts
        )

824-828:  function claimUserReward(
        address token,
        uint256 amount,
        address receiver
    ) external onlyPirexRewards {
```
[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L739-L746](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L739-L746)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L824-L828](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L824-L828)

```
File:  2022-11-redactedcartel/src/vaults/AutoPxGlp.sol

94:  function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

106:   function setPlatformFee(uint256 fee) external onlyOwner {

118:   function setCompoundIncentive(uint256 incentive) external onlyOwner {

130:   function setPlatform(address _platform) external onlyOwner {
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130)


```
File: 2022-11-redactedcartel/src/vaults/AutoPxGmx.sol

104: function setPoolFee(uint24 _poolFee) external onlyOwner {

116:  function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:   function setPlatformFee(uint256 fee) external onlyOwner {

140:   function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:   function setPlatform(address _platform) external onlyOwner {
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152)

[4] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
 
The ``unchecked`` keyword is new in solidity 0.8.0,so this only applies to that version or higher, which these instances are. This saves 30-40 gas *[PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)*

*There are 3 instances of this issue:*

```
File: 2022-11-redactedcartel/src/PirexRewards.sol 

163:  for (uint256 i; i < len; ++i) {

351:   for (uint256 i; i < pLen; ++i) {

396:   for (uint256 i; i < rLen; ++i) {
```

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351)

[https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396)





















