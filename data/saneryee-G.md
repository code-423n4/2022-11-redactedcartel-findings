# Gas Optimizations

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x -y for state variables                                                     |     6     |
| [G-002] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |     1     |
| [G-003] | Duplicated `require()/revert()` checks should be refactored to a modifier or function                                              |    13     |
| [G-004] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |    19     |
| [G-005] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate                 |     1     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x -y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:6

[src/PirexRewards.sol#L361](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L361)

```solidity
361:    producerState.rewardStates[rewardTokens[i]] += r;
```

[src/PxERC20.sol#L85](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PxERC20.sol#L85)

```solidity
85:    balanceOf[msg.sender] -= amount;
```

[src/PxERC20.sol#L124](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PxERC20.sol#L124)

```solidity
124:    balanceOf[to] += amount;
```

[src/PxERC20.sol#L119](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PxERC20.sol#L119)

```solidity
119:    balanceOf[from] -= amount;
```

[src/PxERC20.sol#L124](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PxERC20.sol#L124)

```solidity
124:    balanceOf[to] += amount;
```

[src/vaults/PxGmxReward.sol#L95](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/PxGmxReward.sol#L95)

```solidity
95:    rewardState += rewardAmount;
```


## [G-002] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:1

[src/PirexRewards.sol#L386](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L386)

```solidity
386:    ERC20[] memory rewardTokens = p.rewardTokens;
```

## [G-003] Duplicated `require()/revert()` checks should be refactored to a modifier or function

### Impact

Duplicated `require()/revert()` checks should be refactored to a modifier or function.

### Findings

Total:13

[src/PirexRewards.sol#L467](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L467)

```solidity
467:    if (address(producerToken) == address(0)) revert ZeroAddress();
```

[src/PirexRewards.sol#L468](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L468)

```solidity
468:    if (address(rewardToken) == address(0)) revert ZeroAddress();
```

[src/PirexRewards.sol#L441](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L441)

```solidity
441:    if (recipient == address(0)) revert ZeroAddress();
```

[src/PirexRewards.sol#L467](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L467)

```solidity
467:    if (address(producerToken) == address(0)) revert ZeroAddress();
```

[src/PirexRewards.sol#L375](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L375)

```solidity
375:    if (user == address(0)) revert ZeroAddress();
```

[src/PirexRewards.sol#L466](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L466)

```solidity
466:    if (lpContract.code.length == 0) revert NotContract();
```

[src/PirexGmx.sol#L830](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L830)

```solidity
830:    if (amount == 0) revert ZeroAmount();
```

[src/PirexGmx.sol#L831](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L831)

```solidity
831:    if (receiver == address(0)) revert ZeroAddress();
```

[src/PirexGmx.sol#L829](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L829)

```solidity
829:    if (token == address(0)) revert ZeroAddress();
```

[src/PirexGmx.sol#L728](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L728)

```solidity
728:    if (!gmxVault.whitelistedTokens(token)) revert InvalidToken(token);
```

[src/vaults/AutoPxGlp.sol#L131](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L131)

```solidity
131:    if (_platform == address(0)) revert ZeroAddress();
```

[src/vaults/AutoPxGlp.sol#L421](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L421)

```solidity
421:    if (receiver == address(0)) revert ZeroAddress();
```

[src/vaults/AutoPxGlp.sol#L419](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L419)

```solidity
419:    if (minUsdg == 0) revert ZeroAmount();
```

[src/vaults/AutoPxGmx.sol#L153](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGmx.sol#L153)

```solidity
153:    if (_platform == address(0)) revert ZeroAddress();
```

## [G-004] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:19

[src/PirexRewards.sol#L93](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L93)

```solidity
93:    function setProducer(address _producer) external onlyOwner {
```

[src/PirexRewards.sol#L465](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L465)

```solidity
465:    ) external onlyOwner {
```

[src/PirexRewards.sol#L465](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L465)

```solidity
465:    ) external onlyOwner {
```

[src/PirexGmx.sol#L272](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L272)

```solidity
272:    function configureGmxState() external onlyOwner whenPaused {
```

[src/PirexGmx.sol#L300](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L300)

```solidity
300:    function setFee(Fees f, uint256 fee) external onlyOwner {
```

[src/PirexGmx.sol#L828](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L828)

```solidity
828:    ) external onlyPirexRewards {
```

[src/PirexGmx.sol#L865](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L865)

```solidity
865:    ) external onlyOwner {
```

[src/PirexGmx.sol#L884](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L884)

```solidity
884:    function setVoteDelegate(address voteDelegate) external onlyOwner {
```

[src/PirexGmx.sol#L895](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L895)

```solidity
895:    function clearVoteDelegate() public onlyOwner {
```

[src/PirexGmx.sol#L909](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexGmx.sol#L909)

```solidity
909:    function setPauseState(bool state) external onlyOwner {
```

[src/vaults/AutoPxGlp.sol#L94](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L94)

```solidity
94:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
```

[src/vaults/AutoPxGlp.sol#L106](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L106)

```solidity
106:    function setPlatformFee(uint256 fee) external onlyOwner {
```

[src/vaults/AutoPxGlp.sol#L118](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L118)

```solidity
118:    function setCompoundIncentive(uint256 incentive) external onlyOwner {
```

[src/vaults/AutoPxGlp.sol#L130](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGlp.sol#L130)

```solidity
130:    function setPlatform(address _platform) external onlyOwner {
```

[src/vaults/AutoPxGmx.sol#L104](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGmx.sol#L104)

```solidity
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {
```

[src/vaults/AutoPxGmx.sol#L116](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGmx.sol#L116)

```solidity
116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
```

[src/vaults/AutoPxGmx.sol#L128](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGmx.sol#L128)

```solidity
128:    function setPlatformFee(uint256 fee) external onlyOwner {
```

[src/vaults/AutoPxGmx.sol#L140](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGmx.sol#L140)

```solidity
140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {
```

[src/vaults/AutoPxGmx.sol#L152](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/vaults/AutoPxGmx.sol#L152)

```solidity
152:    function setPlatform(address _platform) external onlyOwner {
```

## [G-005] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:1

[src/PirexRewards.sol#L23-24](https://github.com/code-423n4/2022-11-redactedcartel/tree/main//src/PirexRewards.sol#L23-24)

```solidity
23:            mapping(ERC20 => uint256) rewardStates;
24:            mapping(address => mapping(ERC20 => address)) rewardRecipients;
```


