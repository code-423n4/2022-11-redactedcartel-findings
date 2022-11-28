## [G-01] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-11-redactedcartel/src/PirexGmx.sol::50 => ERC20 public immutable gmxBaseReward; // e.g. WETH (Ethereum)
2022-11-redactedcartel/src/PirexGmx.sol::51 => ERC20 public immutable gmx;
2022-11-redactedcartel/src/PirexGmx.sol::52 => ERC20 public immutable esGmx;
2022-11-redactedcartel/src/PirexGmx.sol::55 => PxERC20 public immutable pxGmx;
2022-11-redactedcartel/src/PirexGmx.sol::56 => PxERC20 public immutable pxGlp;
2022-11-redactedcartel/src/PirexGmx.sol::62 => address public immutable pirexRewards;
2022-11-redactedcartel/src/PirexGmx.sol::65 => DelegateRegistry public immutable delegateRegistry;
2022-11-redactedcartel/src/PxERC20.sol::12 => PirexRewards public immutable pirexRewards;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::30 => address public immutable rewardsModule;
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::33 => ERC20 public immutable gmxBaseReward;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::34 => address public immutable rewardsModule;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::36 => ERC20 public immutable gmxBaseReward;
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::37 => ERC20 public immutable gmx;
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::46 => ERC20 public immutable asset;
```

## [G-02] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

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

## [G-03] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-11-redactedcartel/src/PxGmx.sol::12 => {}
2022-11-redactedcartel/src/PxGmx.sol::23 => {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::276 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::282 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::288 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::294 => ) internal virtual {}
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::300 => ) internal virtual {}
```

## [G-04] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-11-redactedcartel/src/Common.sol::5 => uint32 lastUpdate;
2022-11-redactedcartel/src/Common.sol::11 => uint32 lastUpdate;
2022-11-redactedcartel/src/PirexFees.sol::20 => uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
2022-11-redactedcartel/src/PirexFees.sol::23 => uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
2022-11-redactedcartel/src/PirexFees.sol::28 => uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
2022-11-redactedcartel/src/interfaces/IV3SwapRouter.sol::16 => uint160 sqrtPriceLimitX96;
2022-11-redactedcartel/src/interfaces/IV3SwapRouter.sol::53 => uint160 sqrtPriceLimitX96;
```

## [G-05] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-11-redactedcartel/src/PirexRewards.sol::163 => for (uint256 i; i < len; ++i) {
2022-11-redactedcartel/src/PirexRewards.sol::351 => for (uint256 i; i < pLen; ++i) {
2022-11-redactedcartel/src/PirexRewards.sol::396 => for (uint256 i; i < rLen; ++i) {
```

## [G-06] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::355 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::68 => require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::137 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

## [G-07] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::2 => pragma solidity >=0.8.0;
```

## [G-08] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2022-11-redactedcartel/src/PirexGmx.sol::225 => assert(feeAmount + postFeeAmount == assets);
```

## [G-09] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::217 => function maxDeposit(address) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::221 => function maxMint(address) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::225 => function maxWithdraw(address owner) public view virtual returns (uint256) {
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::229 => function maxRedeem(address owner) public view virtual returns (uint256) {
```

## [G-10] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-11-redactedcartel/src/PirexRewards.sol::22 => mapping(address => UserState) userStates;
2022-11-redactedcartel/src/PirexRewards.sol::24 => mapping(address => mapping(ERC20 => address)) rewardRecipients;
```

## [G-11] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-11-redactedcartel/src/PirexGmx.sol::942 => if (gmxRewardRouterV2.pendingReceivers(address(this)) != address(0))
2022-11-redactedcartel/src/PirexRewards.sol::399 => address recipient = rewardRecipient != address(0)
```

## [G-12] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
2022-11-redactedcartel/src/PirexRewards.sol::216 => UserState memory userState = producerTokens[producerToken].userStates[
```

## [G-13] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::49 => function _globalAccrue() internal {
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::68 => function _userAccrue(address user) internal {
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::90 => function _harvest(uint256 rewardAmount) internal {
```

## [G-14] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::90 => function _harvest(uint256 rewardAmount) internal {
```