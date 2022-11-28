### All capital naming convention should be used for immutable constant values

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L30
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L33
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L36
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L34https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L37
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L46
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L50
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L51
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L52
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L55
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L56
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L62
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L65
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L12

### Typos

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol
PxERC20.sol:87:        // Cannot overflow because the sum of all user
should be 
PxERC20.sol:87:        // Cannot overflow because the sum of all users

PxERC20.sol:121:        // Cannot overflow because the sum of all user
should be 
PxERC20.sol:121:        // Cannot overflow because the sum of all users
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol
AutoPxGlp.sol:189:        // Factor in additional shares to fulfill withdrawal if user is not the last to withdraw
should be 
AutoPxGlp.sol:189:        // Factor in additional shares to fulfil withdrawal if user is not the last to withdraw
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol
AutoPxGmx.sol:211:        // Factor in additional shares to fulfill withdrawal if user is not the last to withdraw
should be 
AutoPxGmx.sol:211:        // Factor in additional shares to fulfil withdrawal if user is not the last to withdraw

use coma after introductory words or phrases
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol
PirexFees.sol:18:    // E.g. if the treasuryFeePercent were set to 50, then the treasury's
should be 
PirexFees.sol:18:    // E.g., if the treasuryFeePercent were set to 50, then the treasury's

PirexGmx.sol:46:    // Fee maximum (i.e. 20%)
should be 
PirexGmx.sol:46:    // Fee maximum (i.e., 20%)

PirexGmx.sol:50:    ERC20 public immutable gmxBaseReward; // e.g. WETH (Ethereum)
should be 
PirexGmx.sol:50:    ERC20 public immutable gmxBaseReward; // e.g., WETH (Ethereum)

PirexGmx.sol:83:    // Fees (e.g. 5000 / 1000000 = 0.5%)
should be 
PirexGmx.sol:83:    // Fees (e.g., 5000 / 1000000 = 0.5%)

PirexRewards.sol:406:                    // Update reward state (i.e. amount) to reflect reward tokens transferred out
should be 
PirexRewards.sol:406:                    // Update reward state (i.e., amount) to reflect reward tokens transferred out

AutoPxGlp.sol:32:    // GMX protocol base reward (e.g. WETH)
should be 
AutoPxGlp.sol:32:    // GMX protocol base reward (e.g., WETH)

AutoPxGmx.sol:384:        // Convert sender GMX into pxGMX and get the post-fee amount (i.e. assets)
should be
AutoPxGmx.sol:384:        // Convert sender GMX into pxGMX and get the post-fee amount (i.e., assets)


Unrequired hyphens
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol
AutoPxGlp.sol:281:            // Required to keep the globalState up-to-date
should be 
AutoPxGlp.sol:281:            // Required to keep the globalState up to date

AutoPxGmx.sol:391:        // and handle it by deducting the recently-deposited assets from the total
should be 
AutoPxGmx.sol:391:        // and handle it by deducting the recently deposited assets from the total

### Missing onlyOwner access control

Missing only owner on initialize function which can currently be called by anyone to transfer ownership to msg.sender
consider adding some kind of access control to this function so that it cannot be called by just anyone

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L85

function initialize() public initializer {
        __Ownable_init();
    }

### Use require over frowned upon assert

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather
 than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its 
documentation states that "The assert function creates an error of type Panic(uint256). ... Properly functioning 
code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your
contract which you should fix".

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225

assert(feeAmount + postFeeAmount == assets);


###  Public functions not called by the contract

Contracts are allowed to override their parents' functions and change the visibility from external to public. Consider changing the vicibility to external for the follwing

PxERC20.sol:113:    ) public override returns (bool) {
PirexRewards.sol:85:    function initialize() public initializer {
PirexRewards.sol:281:    function userAccrue(ERC20 producerToken, address user) public {
PirexGmx.sol:895:    function clearVoteDelegate() public onlyOwner {
PirexERC4626.sol:217:    function maxDeposit(address) public view virtual returns (uint256) {
PirexERC4626.sol:221:    function maxMint(address) public view virtual returns (uint256) {
PirexERC4626.sol:225:    function maxWithdraw(address owner) public view virtual returns (uint256) {
PirexERC4626.sol:229:    function maxRedeem(address owner) public view virtual returns (uint256) {
PirexERC4626.sol:103:    ) public virtual returns (uint256 shares) {
PirexERC4626.sol:128:    ) public virtual returns (uint256 assets) {
PirexERC4626.sol:154:    function totalAssets() public view virtual returns (uint256);
PirexERC4626.sol:217:    function maxDeposit(address) public view virtual returns (uint256) {
PirexERC4626.sol:221:    function maxMint(address) public view virtual returns (uint256) {
PirexERC4626.sol:225:    function maxWithdraw(address owner) public view virtual returns (uint256) {
PirexERC4626.sol:229:    function maxRedeem(address owner) public view virtual returns (uint256) {

### Lack of address(0) checks

The following functions/constructors do not conatain a address(0) check when taking address values, consider adding them so that a 0 value cannot be used (especially if a vlaue is meant to be immutable when set this could require a full relaunch of contract set)
ttps://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L583
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L100
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L554
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxGmx.sol#L10

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L48