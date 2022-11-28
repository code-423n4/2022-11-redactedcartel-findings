

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::2 => pragma solidity >=0.8.0;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-11-redactedcartel/src/PirexRewards.sol::291 => (block.timestamp - u.lastUpdate);
2022-11-redactedcartel/src/PirexRewards.sol::293 => u.lastUpdate = block.timestamp.safeCastTo32();
2022-11-redactedcartel/src/PirexRewards.sol::297 => emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);
2022-11-redactedcartel/src/PirexRewards.sol::314 => if (block.timestamp != lastUpdate || totalSupply != lastSupply) {
2022-11-redactedcartel/src/PirexRewards.sol::316 => (block.timestamp - lastUpdate) *
2022-11-redactedcartel/src/PirexRewards.sol::319 => globalState.lastUpdate = block.timestamp.safeCastTo32();
2022-11-redactedcartel/src/PirexRewards.sol::325 => block.timestamp,
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::54 => (block.timestamp - globalState.lastUpdate) *
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::57 => globalState.lastUpdate = block.timestamp.safeCastTo32();
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::61 => emit GlobalAccrue(block.timestamp, totalSupply, rewards);
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::77 => (block.timestamp - u.lastUpdate);
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::79 => u.lastUpdate = block.timestamp.safeCastTo32();
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::83 => emit UserAccrue(user, block.timestamp, balance, rewards);
```

## [L-03] safeApprove() is deprecated

safeApprove() is deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

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

## [L-04] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::52 => ) ERC20(_name, _symbol, _asset.decimals()) {
```

## [L-05] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2022-11-redactedcartel/src/PirexGmx.sol::225 => assert(feeAmount + postFeeAmount == assets);
```

## [L-06] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-11-redactedcartel/src/PirexGmx.sol::436 => stakedGlp.transferFrom(msg.sender, address(this), amount);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::243 => bool status = ERC20.transfer(to, amount);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::261 => bool status = ERC20.transferFrom(from, to, amount);
```

## [L-07] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-11-redactedcartel/src/PirexGmx.sol::909 => function setPauseState(bool state) external onlyOwner {
2022-11-redactedcartel/src/interfaces/IVault.sol::92 => function setVaultUtils(IVaultUtils _vaultUtils) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::94 => function setError(uint256 _errorCode, string calldata _error) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::143 => function setMaxLeverage(uint256 _maxLeverage) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::145 => function setInManagerMode(bool _inManagerMode) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::147 => function setManager(address _manager, bool _isManager) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::149 => function setIsSwapEnabled(bool _isSwapEnabled) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::151 => function setIsLeverageEnabled(bool _isLeverageEnabled) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::153 => function setMaxGasPrice(uint256 _maxGasPrice) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::155 => function setUsdgAmount(address _token, uint256 _amount) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::157 => function setBufferAmount(address _token, uint256 _amount) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::159 => function setMaxGlobalShortSize(address _token, uint256 _amount) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::161 => function setInPrivateLiquidationMode(bool _inPrivateLiquidationMode)
2022-11-redactedcartel/src/interfaces/IVault.sol::164 => function setLiquidator(address _liquidator, bool _isActive) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::166 => function setFundingRate(
2022-11-redactedcartel/src/interfaces/IVault.sol::172 => function setFees(
2022-11-redactedcartel/src/interfaces/IVault.sol::184 => function setTokenConfig(
2022-11-redactedcartel/src/interfaces/IVault.sol::194 => function setPriceFeed(address _priceFeed) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::12 => function setAdjustment(
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::20 => function setIsAmmEnabled(bool _isEnabled) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::22 => function setIsSecondaryPriceEnabled(bool _isEnabled) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::24 => function setSpreadBasisPoints(address _token, uint256 _spreadBasisPoints)
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::27 => function setSpreadThresholdBasisPoints(uint256 _spreadThresholdBasisPoints)
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::30 => function setFavorPrimaryPrice(bool _favorPrimaryPrice) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::32 => function setPriceSampleSpace(uint256 _priceSampleSpace) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::34 => function setMaxStrictPriceDeviation(uint256 _maxStrictPriceDeviation)
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::51 => function setTokenConfig(
```

## [L-08] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-11-redactedcartel/src/PxERC20.sol::50 => _mint(to, amount);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::317 => _mint(receiver, shares);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::400 => _mint(receiver, shares);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::73 => _mint(receiver, shares);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::92 => _mint(receiver, shares);
```

## [L-09] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2022-11-redactedcartel/src/PirexRewards.sol::4 => import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
```

## [L-10] Implementation contract may not be initialized

Implementation contract does not have a constructor with the initializer modifier therefore may be uninitialized. Implementation contracts should be initialized to avoid potential griefs or exploits.

```
2022-11-redactedcartel/src/PirexRewards.sol::4 => import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
```

## [N-1] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::2 => pragma solidity >=0.8.0;
```

## [N-2] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2022-11-redactedcartel/src/PirexGmx.sol::83 => // Fees (e.g. 5000 / 1000000 = 0.5%)
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::20 => uint256 public constant FEE_DENOMINATOR = 10000;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::22 => uint256 public constant FEE_DENOMINATOR = 10000;
```

## [N-3] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-11-redactedcartel/src/PirexFees.sol::34 => event SetFeeRecipient(FeeRecipient f, address recipient);
2022-11-redactedcartel/src/PirexFees.sol::35 => event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
2022-11-redactedcartel/src/PirexGmx.sol::140 => event InitiateMigration(address newContract);
2022-11-redactedcartel/src/PirexGmx.sol::141 => event CompleteMigration(address oldContract);
2022-11-redactedcartel/src/PirexGmx.sol::142 => event SetDelegationSpace(string delegationSpace, bool shouldClear);
2022-11-redactedcartel/src/PirexGmx.sol::143 => event SetVoteDelegate(address voteDelegate);
2022-11-redactedcartel/src/PirexGmx.sol::144 => event ClearVoteDelegate();
2022-11-redactedcartel/src/PirexRewards.sol::33 => event SetProducer(address producer);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::35 => event WithdrawalPenaltyUpdated(uint256 penalty);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::36 => event PlatformFeeUpdated(uint256 fee);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::37 => event CompoundIncentiveUpdated(uint256 incentive);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::38 => event PlatformUpdated(address _platform);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::39 => event PoolFeeUpdated(uint24 _poolFee);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::40 => event WithdrawalPenaltyUpdated(uint256 penalty);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::41 => event PlatformFeeUpdated(uint256 fee);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::42 => event CompoundIncentiveUpdated(uint256 incentive);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::43 => event PlatformUpdated(address _platform);
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::21 => event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::28 => event Harvest(uint256 rewardAmount);
```

## [N-4] Missing NatSpec

Code should include NatSpec

```
2022-11-redactedcartel/src/Common.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IAutoPxGlp.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IBasePositionManager.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IGMX.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IGlpManager.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IPirexRewards.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IProducer.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IRewardDistributor.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IRewardRouterV2.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IStakedGlp.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/ITimelock.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IVault.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::1 => // SPDX-License-Identifier: MIT
2022-11-redactedcartel/src/interfaces/IWETH.sol::1 => // SPDX-License-Identifier: MIT
```

## [N-5] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-11-redactedcartel/src/PirexRewards.sol::85 => function initialize() public initializer {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::217 => function maxDeposit(address) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::221 => function maxMint(address) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::225 => function maxWithdraw(address owner) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::229 => function maxRedeem(address owner) public view virtual returns (uint256) {
```

## [N-6] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-11-redactedcartel/src/PirexGmx.sol::909 => function setPauseState(bool state) external onlyOwner {
2022-11-redactedcartel/src/interfaces/IVault.sol::92 => function setVaultUtils(IVaultUtils _vaultUtils) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::94 => function setError(uint256 _errorCode, string calldata _error) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::143 => function setMaxLeverage(uint256 _maxLeverage) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::145 => function setInManagerMode(bool _inManagerMode) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::147 => function setManager(address _manager, bool _isManager) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::149 => function setIsSwapEnabled(bool _isSwapEnabled) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::151 => function setIsLeverageEnabled(bool _isLeverageEnabled) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::153 => function setMaxGasPrice(uint256 _maxGasPrice) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::155 => function setUsdgAmount(address _token, uint256 _amount) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::157 => function setBufferAmount(address _token, uint256 _amount) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::159 => function setMaxGlobalShortSize(address _token, uint256 _amount) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::161 => function setInPrivateLiquidationMode(bool _inPrivateLiquidationMode)
2022-11-redactedcartel/src/interfaces/IVault.sol::164 => function setLiquidator(address _liquidator, bool _isActive) external;
2022-11-redactedcartel/src/interfaces/IVault.sol::166 => function setFundingRate(
2022-11-redactedcartel/src/interfaces/IVault.sol::172 => function setFees(
2022-11-redactedcartel/src/interfaces/IVault.sol::184 => function setTokenConfig(
2022-11-redactedcartel/src/interfaces/IVault.sol::194 => function setPriceFeed(address _priceFeed) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::12 => function setAdjustment(
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::20 => function setIsAmmEnabled(bool _isEnabled) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::22 => function setIsSecondaryPriceEnabled(bool _isEnabled) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::24 => function setSpreadBasisPoints(address _token, uint256 _spreadBasisPoints)
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::27 => function setSpreadThresholdBasisPoints(uint256 _spreadThresholdBasisPoints)
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::30 => function setFavorPrimaryPrice(bool _favorPrimaryPrice) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::32 => function setPriceSampleSpace(uint256 _priceSampleSpace) external;
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::34 => function setMaxStrictPriceDeviation(uint256 _maxStrictPriceDeviation)
2022-11-redactedcartel/src/interfaces/IVaultPriceFeed.sol::51 => function setTokenConfig(
```

## [N-7] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2022-11-redactedcartel/src/PxERC20.sol::9 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2022-11-redactedcartel/src/PxERC20.sol::10 => bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```
