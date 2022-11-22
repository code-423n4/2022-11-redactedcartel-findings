
### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::28 => uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
2022-11-redactedcartel/src/PirexFees.sol::31 => address public treasury;
2022-11-redactedcartel/src/PirexFees.sol::32 => address public contributors;
2022-11-redactedcartel/src/PirexGmx.sol::59 => PirexFees public pirexFees;
2022-11-redactedcartel/src/PirexGmx.sol::68 => IRewardRouterV2 public gmxRewardRouterV2;
2022-11-redactedcartel/src/PirexGmx.sol::69 => RewardTracker public rewardTrackerGmx;
2022-11-redactedcartel/src/PirexGmx.sol::70 => RewardTracker public rewardTrackerGlp;
2022-11-redactedcartel/src/PirexGmx.sol::71 => RewardTracker public feeStakedGlp;
2022-11-redactedcartel/src/PirexGmx.sol::72 => RewardTracker public stakedGmx;
2022-11-redactedcartel/src/PirexGmx.sol::73 => IStakedGlp public stakedGlp;
2022-11-redactedcartel/src/PirexGmx.sol::74 => address public glpManager;
2022-11-redactedcartel/src/PirexGmx.sol::75 => IVault public gmxVault;
2022-11-redactedcartel/src/PirexGmx.sol::78 => address public migratedTo;
2022-11-redactedcartel/src/PirexRewards.sol::28 => IProducer public producer;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::27 => address public platform;
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::15 => ERC20 public pxGmx;
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::17 => GlobalState public globalState;
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::18 => uint256 public rewardState;
```




### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::31 => address public treasury;
2022-11-redactedcartel/src/PirexFees.sol::32 => address public contributors;
2022-11-redactedcartel/src/PirexGmx.sol::74 => address public glpManager;
2022-11-redactedcartel/src/PirexGmx.sol::78 => address public migratedTo;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::27 => address public platform;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::31 => address public platform;
```


### [G03] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-11-redactedcartel/src/PirexRewards.sol::163 => for (uint256 i; i < len; ++i) {
2022-11-redactedcartel/src/PirexRewards.sol::396 => for (uint256 i; i < rLen; ++i) {
```


### [G04] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::569 => return _depositGlp(address(0), msg.value, minUsdg, minGlp, receiver);
2022-11-redactedcartel/src/PirexGmx.sol::602 => return _depositGlp(token, tokenAmount, minUsdg, minGlp, receiver);
2022-11-redactedcartel/src/PirexGmx.sol::699 => return _redeemPxGlp(address(0), amount, minOut, receiver);
2022-11-redactedcartel/src/PirexGmx.sol::730 => return _redeemPxGlp(token, amount, minOut, receiver);
2022-11-redactedcartel/src/PirexRewards.sol::334 => @return _producerTokens  ERC20[]  Producer token contracts
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::355 => return _deposit(assets, receiver);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::403 => return _deposit(assets, receiver);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::430 => return _deposit(assets, receiver);
```




### [G05] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-11-redactedcartel/src/PirexRewards.sol::391 => p.userStates[user].rewards = 0;
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::118 => userRewardStates[msg.sender].rewards = 0;
```



### [G06] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-11-redactedcartel/src/Common.sol::5 => uint32 lastUpdate;
2022-11-redactedcartel/src/Common.sol::6 => uint224 lastSupply;
2022-11-redactedcartel/src/Common.sol::11 => uint32 lastUpdate;
2022-11-redactedcartel/src/Common.sol::12 => uint224 lastBalance;
2022-11-redactedcartel/src/PirexFees.sol::20 => uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
2022-11-redactedcartel/src/PirexFees.sol::23 => uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
2022-11-redactedcartel/src/PirexFees.sol::28 => uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
2022-11-redactedcartel/src/PirexFees.sol::35 => event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
2022-11-redactedcartel/src/PirexFees.sol::83 => function setTreasuryFeePercent(uint8 _treasuryFeePercent)
2022-11-redactedcartel/src/PxERC20.sol::28 => uint8 _decimals
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::26 => uint24 public poolFee = 3000;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::46 => uint24 fee,
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::48 => uint160 sqrtPriceLimitX96,
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::102 => @param  _poolFee  uint24  Uniswap pool fee
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::104 => function setPoolFee(uint24 _poolFee) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::243 => uint24 fee,
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::245 => uint160 sqrtPriceLimitX96,
```




### [G07] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detailed description of the issue
#### Findings:
```
2022-11-redactedcartel/src/PxERC20.sol::9 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2022-11-redactedcartel/src/PxERC20.sol::10 => bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```







### [G08] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::355 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::68 => require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::137 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```




### [G09] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::272 => function configureGmxState() external onlyOwner whenPaused {
2022-11-redactedcartel/src/PirexGmx.sol::300 => function setFee(Fees f, uint256 fee) external onlyOwner {
2022-11-redactedcartel/src/PirexGmx.sol::884 => function setVoteDelegate(address voteDelegate) external onlyOwner {
2022-11-redactedcartel/src/PirexGmx.sol::895 => function clearVoteDelegate() public onlyOwner {
2022-11-redactedcartel/src/PirexGmx.sol::909 => function setPauseState(bool state) external onlyOwner {
2022-11-redactedcartel/src/PirexRewards.sol::93 => function setProducer(address _producer) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::94 => function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::106 => function setPlatformFee(uint256 fee) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::118 => function setCompoundIncentive(uint256 incentive) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::130 => function setPlatform(address _platform) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::104 => function setPoolFee(uint24 _poolFee) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::116 => function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::128 => function setPlatformFee(uint256 fee) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::140 => function setCompoundIncentive(uint256 incentive) external onlyOwner {
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::152 => function setPlatform(address _platform) external onlyOwner {
```


### [G10] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here:
#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::114 => token.safeTransfer(treasury, treasuryDistribution);
2022-11-redactedcartel/src/PirexFees.sol::115 => token.safeTransfer(contributors, contributorsDistribution);
2022-11-redactedcartel/src/PirexGmx.sol::436 => stakedGlp.transferFrom(msg.sender, address(this), amount);
2022-11-redactedcartel/src/PirexGmx.sol::844 => gmxBaseReward.safeTransfer(receiver, postFeeAmount);
2022-11-redactedcartel/src/PirexGmx.sol::847 => gmxBaseReward.safeTransfer(address(pirexFees), feeAmount);
2022-11-redactedcartel/src/PirexGmx.sol::946 => gmxBaseReward.safeTransfer(
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::258 => asset.safeTransfer(msg.sender, pxGlpIncentive);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::260 => asset.safeTransfer(owner, totalPxGlpFee - pxGlpIncentive);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::274 => pxGmx.safeTransfer(msg.sender, pxGmxIncentive);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::276 => pxGmx.safeTransfer(owner, totalPxGmxFee - pxGmxIncentive);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::297 => if (incentive != 0) asset.safeTransfer(msg.sender, incentive);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::299 => asset.safeTransfer(owner, totalFee - incentive);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::336 => asset.safeTransfer(receiver, assets);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::361 => asset.safeTransfer(receiver, assets);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::119 => asset.safeTransfer(receiver, assets);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::145 => asset.safeTransfer(receiver, assets);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::243 => bool status = ERC20.transfer(to, amount);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::261 => bool status = ERC20.transferFrom(from, to, amount);
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::128 => pxGmx.safeTransfer(receiver, amount);
```




### [G11] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable getter functions for deployment call data, and not adding another entry to the method ID table

#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::20 => uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
2022-11-redactedcartel/src/PirexFees.sol::23 => uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
2022-11-redactedcartel/src/PirexGmx.sol::44 => uint256 public constant FEE_DENOMINATOR = 1_000_000;
2022-11-redactedcartel/src/PirexGmx.sol::47 => uint256 public constant FEE_MAX = 200_000;
2022-11-redactedcartel/src/PxERC20.sol::9 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2022-11-redactedcartel/src/PxERC20.sol::10 => bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::18 => uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::19 => uint256 public constant MAX_PLATFORM_FEE = 2000;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::20 => uint256 public constant FEE_DENOMINATOR = 10000;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::21 => uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::22 => uint256 public constant EXPANDED_DECIMALS = 1e30;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::18 => IV3SwapRouter public constant SWAP_ROUTER =
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::20 => uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::21 => uint256 public constant MAX_PLATFORM_FEE = 2000;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::22 => uint256 public constant FEE_DENOMINATOR = 10000;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::23 => uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
```




### [G12] Usage of `assert()` instead of `require()`

#### Impact
Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions).require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require, unless a Panic really is intended.
#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::225 => assert(feeAmount + postFeeAmount == assets);
```



### [G13] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-11-redactedcartel/src/PxGmx.sol::12 => {}
2022-11-redactedcartel/src/PxGmx.sol::23 => {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::276 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::282 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::288 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::294 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::300 => ) internal virtual {}
```




### [G14] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. 
#### Findings:
```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::19 => abstract contract PirexERC4626 is ERC20 {
```


