# For loop ++i unchecked

Each time i++ is called under/overflow checks are made.
But we're already constraining i by length, `i < length`, making those under/overflow checks unnecessary. 

```
for (uint256 i; i < length;) {
  ...
  unchecked{ ++i; }
}
```

```
File: src/PirexRewards.sol

163:    for (uint256 i; i < len; ++i) {     

351:    for (uint256 i; i < pLen; ++i) {

396:    for (uint256 i; i < rLen; ++i) {
```

# X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

```
File: src/PirexRewards.sol

361:   producerState.rewardStates[rewardTokens[i]] += r;
```

```
File: src/PxERC20.sol

85:   balanceOf[msg.sender] -= amount;
90:   balanceOf[to] += amount;
119:  balanceOf[from] -= amount;
124:  balanceOf[to] += amount;
```


```
File: src/vaults/PxGmxReward.sol

95:   rewardState += rewardAmount;
```

# STORAGE POINTER TO A STRUCTURE IS CHEAPER THAN COPYING EACH VALUE OF THE STRUCTURE INTO MEMORY, SAME FOR ARRAY AND MAPPING

```
diff --git a/a/src/PirexRewards.sol b/b/src/PirexRewards.sol
index 59c5ff7..76874de 100644
--- a/a/src/PirexRewards.sol
+++ b/b/src/PirexRewards.sol
@@ -216 +216 @@ contract PirexRewards is OwnableUpgradeable {
-        UserState memory userState = producerTokens[producerToken].userStates[
+        UserState storage userState = producerTokens[producerToken].userStates[
```

`forge snapshot --diff`: 81 Gas Saved

# INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
File: src/vaults/AutoPxGlp.sol

279:   _harvest(pxGmxAmountOut - totalPxGmxFee);

File: src/vaults/PxGmxReward.sol

90:   function _harvest(uint256 rewardAmount) internal {
91:           // Update global reward accrual state and associate with the update of reward state
92:           _globalAccrue();
93:   
94:           if (rewardAmount != 0) {
95:               rewardState += rewardAmount;
96:   
97:               emit Harvest(rewardAmount);
98:           }
99:       }
```

#  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```
File: src/PirexFees.sol
63:     function setFeeRecipient(FeeRecipient f, address recipient)
64:         external
65:         onlyOwner
66:     {

File: src/PirexFees.sol
83:     function setTreasuryFeePercent(uint8 _treasuryFeePercent)
84:         external
85:         onlyOwner
86:     {

File: src/PirexGmx.sol
272:     function configureGmxState() external onlyOwner whenPaused {

File: src/PirexGmx.sol
300:     function setFee(Fees f, uint256 fee) external onlyOwner {

File: src/PirexGmx.sol
313:     function setContract(Contracts c, address contractAddress)
314:         external
315:         onlyOwner
316:     {

File: src/PirexGmx.sol
862:     function setDelegationSpace(
863:         string memory _delegationSpace,
864:         bool shouldClear
865:     ) external onlyOwner {

File: src/PirexGmx.sol
884:     function setVoteDelegate(address voteDelegate) external onlyOwner {

File: src/PirexGmx.sol
895:     function clearVoteDelegate() public onlyOwner {

File: src/PirexGmx.sol
909:     function setPauseState(bool state) external onlyOwner {

File: src/PirexGmx.sol
921:     function initiateMigration(address newContract)
922:         external
923:         whenPaused
924:         onlyOwner
925:     {

File: src/PirexGmx.sol
956:     function completeMigration(address oldContract)
957:         external
958:         whenPaused
959:         onlyOwner
960:     {

File: src/PirexRewards.sol
93:     function setProducer(address _producer) external onlyOwner {


File: src/PirexRewards.sol
151:     function addRewardToken(ERC20 producerToken, ERC20 rewardToken)
152:         external
153:         onlyOwner
154:     {

File: src/PirexRewards.sol
179:     function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
180:         external
181:         onlyOwner
182:     {

File: src/PirexRewards.sol
432:     function setRewardRecipientPrivileged(
433:         address lpContract,
434:         ERC20 producerToken,
435:         ERC20 rewardToken,
436:         address recipient
437:     ) external onlyOwner {

File: src/PirexRewards.sol
461:     function unsetRewardRecipientPrivileged(
462:         address lpContract,
463:         ERC20 producerToken,
464:         ERC20 rewardToken
465:     ) external onlyOwner {

File: src/vaults/AutoPxGlp.sol
94:     function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

File: src/vaults/AutoPxGlp.sol
106:     function setPlatformFee(uint256 fee) external onlyOwner {

File: src/vaults/AutoPxGlp.sol
118:     function setCompoundIncentive(uint256 incentive) external onlyOwner {

File: src/vaults/AutoPxGlp.sol
130:     function setPlatform(address _platform) external onlyOwner {

File: src/vaults/AutoPxGmx.sol
104:     function setPoolFee(uint24 _poolFee) external onlyOwner {

File: src/vaults/AutoPxGmx.sol
116:     function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

File: src/vaults/AutoPxGmx.sol
128:     function setPlatformFee(uint256 fee) external onlyOwner {

File: src/vaults/AutoPxGmx.sol
140:     function setCompoundIncentive(uint256 incentive) external onlyOwner {

File: src/vaults/AutoPxGmx.sol
152:     function setPlatform(address _platform) external onlyOwner {


```
