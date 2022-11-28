## [G-01] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue
Constant expressions are left as expressions, not constants.
It is re-calculated each time it is in use.
Each usage of such "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..)
### Findings
```solidity
src/PxERC20.sol
9:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
10:     bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```
### Mitigation
Use `immutable` instead of  `constant`

## [G-02]. `x += y` costs more gas than `x = x + y` for state variables
`x += y` costs more than `x = x + y`
same as `x -= y`
### Findings
```solidity
src/PirexRewards.sol
361:         producerState.rewardStates[rewardTokens[i]] += r;

src/PxERC20.sol
85:         balanceOf[msg.sender] -= amount;

90:         balanceOf[to] += amount;

119:        balanceOf[from] -= amount;

124:        balanceOf[to] += amount;

src/vaults/PxGmxReward.sol
95:             rewardState += rewardAmount;
```
### Mitigation
Replace `x += y` and `x -= y` with `x = x + y` and `x = x - y`.


## [G-03] Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead
See the warning at this link: <https://docs.soliditylang.org/en/v0.8.0/internals/layout_in_storage.html#layout-of-state-variables-in-storage> :
> When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
> It is only beneficial to use reduced-size arguments if you are dealing with storage values because the compiler will pack multiple elements into one storage slot, and thus, combine multiple reads or writes into a single operation. When dealing with function arguments or memory values, there is no inherent benefit because the compiler does not pack these values.

### Findings
```solidity
src/vaults/
AutoPxGmx.sol
26:     uint24 public poolFee = 3000;

39:     event PoolFeeUpdated(uint24 _poolFee);

46:         uint24 fee,

104:     function setPoolFee(uint24 _poolFee) external onlyOwner {


src/PirexFees.sol
20:     uint8 public constant FEE_PERCENT_DENOMINATOR = 100;

23:     uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

28:     uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;

35:     event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
```

### Mitigation
Generally use the `uint256` type and convert it to smaller type when you want to store a value.

## [G-04] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Findings
```solidity
src/vaults/AutoPxGlp.sol
94:     function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

106:     function setPlatformFee(uint256 fee) external onlyOwner {

118:     function setCompoundIncentive(uint256 incentive) external onlyOwner {

130:     function setPlatform(address _platform) external onlyOwner {

src/vaults/AutoPxGmx.sol
104:     function setPoolFee(uint24 _poolFee) external onlyOwner {

116:     function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:     function setPlatformFee(uint256 fee) external onlyOwner {

140:     function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:     function setPlatform(address _platform) external onlyOwner {

src/PirexFees.sol
63:     function setFeeRecipient(FeeRecipient f, address recipient)
64:         external
65:         onlyOwner
66:     {

83:     function setTreasuryFeePercent(uint8 _treasuryFeePercent)
84:         external
85:         onlyOwner
86:     {

src/PirexGmx.sol
272:     function configureGmxState() external onlyOwner whenPaused {

300:     function setFee(Fees f, uint256 fee) external onlyOwner {

313:     function setContract(Contracts c, address contractAddress)
314:         external
315:         onlyOwner
316:     {

862:     function setDelegationSpace(
863:         string memory _delegationSpace,
864:         bool shouldClear
865:     ) external onlyOwner {

884:     function setVoteDelegate(address voteDelegate) external onlyOwner {

895:     function clearVoteDelegate() public onlyOwner {

909:     function setPauseState(bool state) external onlyOwner {

921:     function initiateMigration(address newContract)
922:         external
923:         whenPaused
924:         onlyOwner
925:     {

956:     function completeMigration(address oldContract)
957:         external
958:         whenPaused
959:         onlyOwner
960:     {


src/PirexRewards.sol
93:     function setProducer(address _producer) external onlyOwner {

151:     function addRewardToken(ERC20 producerToken, ERC20 rewardToken)
152:         external
153:         onlyOwner
154:     {

179:     function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
180:         external
181:         onlyOwner
182:     {

432:     function setRewardRecipientPrivileged(
433:         address lpContract,
434:         ERC20 producerToken,
435:         ERC20 rewardToken,
436:         address recipient
437:     ) external onlyOwner {

461:     function unsetRewardRecipientPrivileged(
462:         address lpContract,
463:         ERC20 producerToken,
464:         ERC20 rewardToken
465:     ) external onlyOwner {

src/PxERC20.sol
45:     function mint(address to, uint256 amount)
46:         external
47:         virtual
48:         onlyRole(MINTER_ROLE)
49:     {

62:     function burn(address from, uint256 amount)
63:         external
64:         virtual
65:         onlyRole(BURNER_ROLE)
66:     {

src/PxGmx.sol
19:     function burn(address from, uint256 amount)
20:         external
21:         override
22:         onlyRole(BURNER_ROLE)
23:     {}



```
### Mitigation 
Add `payable` keyword to a function declaration

## [G-05] Unchecked arithmetic
The default “checked” behavior costs more gas when adding/diving/multiplying, because under-the-hood those checks are implemented as a series of opcodes that, prior to performing the actual arithmetic, check for under/overflow and revert if it is detected.
if it can statically be determined there is no possible way for your arithmetic to under/overflow (such as a condition in an if statement), surrounding the arithmetic in an unchecked block will save gas.
### Findings
```
src/PirexRewards.sol
163:         for (uint256 i; i < len; ++i) {

351:         for (uint256 i; i < pLen; ++i) {

396:             for (uint256 i; i < rLen; ++i) {
```

```
src/vaults/AutoPxGlp.sol
168:         return assets - penalty;
```
`penalty` is 0 or something smaller than assets.

```
src/vaults/AutoPxGlp.sol
194:  (FEE_DENOMINATOR - withdrawalPenalty);
```
`FEE_DENOMINATOR` is always greater than `withdrawalPenalty`

```
src/vaults/AutoPxGlp.sol
250:         uint256 newAssets = totalAssets() - preClaimTotalAssets;
```
`totalAssets()` is always >= `preClaimTotalAssets`

```
src/vaults/AutoPxGlp.sol
264:         pxGmxAmountOut = pxGmx.balanceOf(address(this)) - preClaimPxGmxAmount;
```
`pxGmx.balanceOf(address(this))` is always >= `preClaimPxGmxAmount`

```
src/vaults/AutoPxGlp.sol
276:             pxGmx.safeTransfer(owner, totalPxGmxFee - pxGmxIncentive);

279:             _harvest(pxGmxAmountOut - totalPxGmxFee);
```


```
src/vaults/AutoPxGmx.sol
190:         return assets - penalty;

216:         (FEE_DENOMINATOR - withdrawalPenalty);

288:         if ((totalAssets() - assetsBeforeClaim) != 0) {

299:         asset.safeTransfer(owner, totalFee - incentive);
```


```
src/vaults/PxGmxReward.sol
117:             globalState.rewards = globalRewards - userRewards;

126:                 rewardState = _rewardState - amount;

```


```
src/PirexFees.sol
104:         uint256 contributorsDistribution = distribution - treasuryDistribution;
```

```
src/PirexGmx.sol
223:         postFeeAmount = assets - feeAmount;

225:         assert(feeAmount + postFeeAmount == assets);
```

```
src/PirexRewards.sol
390:             p.globalState.rewards = globalRewards - userRewards;

407:                     p.rewardStates[rewardToken] = rewardState - amount;

```
### Mitigation
Place the arithmetic operations in an `unchecked` block
```
	for (uint i; i < length;) {
		...
		unchecked{ ++i; }
	}
```

## [G-06] Obsolete action
There is no need for this `assert`:
### Findings
```
src/PirexGmx.sol
225:         assert(feeAmount + postFeeAmount == assets);
```
Since `feeAmount + postFeeAmount` is always == `assets`, just waste of gas by doing this.
### Mitigation
Remove it.