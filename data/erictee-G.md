### [G-01] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/PxGmx.sol:L12    {}

src/PxGmx.sol:L23    {}

src/vaults/PirexERC4626.sol:L276    ) internal virtual {}

src/vaults/PirexERC4626.sol:L282    ) internal virtual {}

src/vaults/PirexERC4626.sol:L288    ) internal virtual {}

src/vaults/PirexERC4626.sol:L294    ) internal virtual {}

src/vaults/PirexERC4626.sol:L300    ) internal virtual {}

```

### [G-02] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
src/vaults/PirexERC4626.sol:L2      pragma solidity >=0.8.0;

```

### [G-03] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
src/PirexRewards.sol:L93    function setProducer(address _producer) external onlyOwner {

src/PirexGmx.sol:L272    function configureGmxState() external onlyOwner whenPaused {

src/PirexGmx.sol:L300    function setFee(Fees f, uint256 fee) external onlyOwner {

src/PirexGmx.sol:L884    function setVoteDelegate(address voteDelegate) external onlyOwner {

src/PirexGmx.sol:L895    function clearVoteDelegate() public onlyOwner {

src/PirexGmx.sol:L909    function setPauseState(bool state) external onlyOwner {

src/vaults/AutoPxGlp.sol:L94    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

src/vaults/AutoPxGlp.sol:L106    function setPlatformFee(uint256 fee) external onlyOwner {

src/vaults/AutoPxGlp.sol:L118    function setCompoundIncentive(uint256 incentive) external onlyOwner {

src/vaults/AutoPxGlp.sol:L130    function setPlatform(address _platform) external onlyOwner {

src/vaults/AutoPxGmx.sol:L104    function setPoolFee(uint24 _poolFee) external onlyOwner {

src/vaults/AutoPxGmx.sol:L116    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

src/vaults/AutoPxGmx.sol:L128    function setPlatformFee(uint256 fee) external onlyOwner {

src/vaults/AutoPxGmx.sol:L140    function setCompoundIncentive(uint256 incentive) external onlyOwner {

src/vaults/AutoPxGmx.sol:L152    function setPlatform(address _platform) external onlyOwner {

```

### [G-04] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/vaults/PxGmxReward.sol:L95            rewardState += rewardAmount;

```

### [G-05] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
src/PirexRewards.sol:L163        for (uint256 i; i < len; ++i) {

src/PirexRewards.sol:L351        for (uint256 i; i < pLen; ++i) {

src/PirexRewards.sol:L396            for (uint256 i; i < rLen; ++i) {

```


