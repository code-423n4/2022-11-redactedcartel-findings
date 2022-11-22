

### [L01] require() should be used instead of assert()


#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::225 => assert(feeAmount + postFeeAmount == assets);
```



### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables



#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::54 => treasury = _treasury;
2022-11-redactedcartel/src/PirexFees.sol::55 => contributors = _contributors;
2022-11-redactedcartel/src/PirexFees.sol::91 => treasuryFeePercent = _treasuryFeePercent;
2022-11-redactedcartel/src/PirexGmx.sol::196 => pirexRewards = _pirexRewards;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::83 => platform = _platform;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::84 => rewardsModule = _rewardsModule;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::133 => platform = _platform;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::92 => platform = _platform;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::93 => rewardsModule = _rewardsModule;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::107 => poolFee = _poolFee;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::155 => platform = _platform;
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::53 => asset = _asset;
```



### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-11-redactedcartel/src/PirexRewards.sol::85 => function initialize() public initializer {
```



### [L04] `safeApprove()` is deprecated

#### Impact
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of safeIncreaseAllowance() and safeDecreaseAllowance()
#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::292 => gmx.safeApprove(address(stakedGmx), type(uint256).max);
2022-11-redactedcartel/src/PirexGmx.sol::348 => gmx.safeApprove(address(stakedGmx), 0);
2022-11-redactedcartel/src/PirexGmx.sol::353 => gmx.safeApprove(contractAddress, type(uint256).max);
2022-11-redactedcartel/src/PirexGmx.sol::507 => t.safeApprove(glpManager, tokenAmount);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::87 => gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::347 => stakedGlp.safeApprove(platform, amount);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::391 => erc20Token.safeApprove(platform, tokenAmount);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::96 => gmxBaseReward.safeApprove(address(SWAP_ROUTER), type(uint256).max);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::97 => gmx.safeApprove(_platform, type(uint256).max);
```



### [L05] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

#### Impact
See [this link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
#### Findings:
```
2022-11-redactedcartel/src/PirexRewards.sol::15 => contract PirexRewards is OwnableUpgradeable {
```



### [L06] Return values of `transfer()`/`transferFrom()` not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have been marked as failed may 
potentially go through without actually making a payment
#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::436 => stakedGlp.transferFrom(msg.sender, address(this), amount);
```




### Non-Critical Issues


### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::247 => return status;
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::265 => return status;
```





### [N02] constants should be defined rather than using magic numbers


#### Findings:
```
2022-11-redactedcartel/src/PxGmx.sol::11 => PxERC20(_pirexRewards, "Pirex GMX", "pxGMX", 18)
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::18 => uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::20 => uint256 public constant FEE_DENOMINATOR = 10000;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::21 => uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::20 => uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::22 => uint256 public constant FEE_DENOMINATOR = 10000;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::23 => uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
```




### [N03] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::34 => event SetFeeRecipient(FeeRecipient f, address recipient);
2022-11-redactedcartel/src/PirexGmx.sol::142 => event SetDelegationSpace(string delegationSpace, bool shouldClear);
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::21 => event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);
```




### [N04] Unused file


#### Findings:
```
2022-11-redactedcartel/src/Common.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/PirexFees.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/PirexGmx.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/PirexRewards.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/PxERC20.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/PxGmx.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IAutoPxGlp.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IPirexRewards.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IProducer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::1 => // SPDX-License-Identifier: MIT
```




### [N05] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```

2022-11-redactedcartel/src/PirexGmx.sol::895 => function clearVoteDelegate() public onlyOwner {
2022-11-redactedcartel/src/PirexRewards.sol::85 => function initialize() public initializer {
2022-11-redactedcartel/src/PirexRewards.sol::281 => function userAccrue(ERC20 producerToken, address user) public {
2022-11-redactedcartel/src/PxERC20.sol::113 => ) public override returns (bool) {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::142 => function totalAssets() public view override returns (uint256) {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::440 => ) public override returns (uint256 shares) {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::453 => ) public override returns (uint256 assets) {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::164 => function totalAssets() public view override returns (uint256) {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::319 => ) public override returns (uint256 shares) {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::343 => ) public override returns (uint256 assets) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::103 => ) public virtual returns (uint256 shares) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::128 => ) public virtual returns (uint256 assets) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::154 => function totalAssets() public view virtual returns (uint256);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::187 => function previewMint(uint256 shares) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::217 => function maxDeposit(address) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::221 => function maxMint(address) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::225 => function maxWithdraw(address owner) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::229 => function maxRedeem(address owner) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::260 => ) public override returns (bool) {
```



### [N06] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-11-redactedcartel/src/PirexRewards.sol::312 => // Calculate rewards, the product of seconds elapsed and last supply
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::52 => // Calculate rewards, the product of seconds elapsed and last supply
```







