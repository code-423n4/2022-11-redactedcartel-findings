## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data.

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient);
35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
63:    event Harvest(
```
## Complementary Codehash Checks
Consider adding an optional codehash check for each contract zero address check since the latter cannot guarantee a matching address has been inputted.

For instance, the following code block may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L50-L56

```
    constructor(address _treasury, address _contributors, bytes32 _treasuryCodeHash, bytes32 _contributorsCodeHash) Owned(msg.sender) {
        if (_treasury == address(0) || _treasury.codehash != _treasuryCodeHash) revert ZeroAddress();
        if (_contributors == address(0) || _contributors.codehash != _contributorsCodeHash) revert ZeroAddress();

        treasury = _treasury;
        contributors = _contributors;
    }
```
All other instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
67:        if (recipient == address(0)) revert ZeroAddress();
```
[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
30:        if (_pirexRewards == address(0)) revert ZeroAddress();
```
[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
41:        if (_pxGmx == address(0)) revert ZeroAddress();
```
[File:AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
82:        if (_gmxBaseReward == address(0)) revert ZeroAddress();
83:        if (_gmx == address(0)) revert ZeroAddress();
84:        if (_asset == address(0)) revert ZeroAddress();

87:        if (_platform == address(0)) revert ZeroAddress();
88:        if (_rewardsModule == address(0)) revert ZeroAddress();

153:        if (_platform == address(0)) revert ZeroAddress();
```
[File: AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
75:        if (_gmxBaseReward == address(0)) revert ZeroAddress();
76:        if (_asset == address(0)) revert ZeroAddress();

79:        if (_platform == address(0)) revert ZeroAddress();
80:        if (_rewardsModule == address(0)) revert ZeroAddress();

131:        if (_platform == address(0)) revert ZeroAddress();

374:        if (token == address(0)) revert ZeroAddress();
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
94:        if (_producer == address(0)) revert ZeroAddress();

112:        if (address(producerToken) == address(0)) revert ZeroAddress();
113:        if (address(rewardToken) == address(0)) revert ZeroAddress();

136:        if (address(producerToken) == address(0)) revert ZeroAddress();
137:        if (address(rewardToken) == address(0)) revert ZeroAddress();

155:        if (address(producerToken) == address(0)) revert ZeroAddress();
156:        if (address(rewardToken) == address(0)) revert ZeroAddress();

183:        if (address(producerToken) == address(0)) revert ZeroAddress();

271:        if (address(producerToken) == address(0)) revert ZeroAddress();

282:        if (address(producerToken) == address(0)) revert ZeroAddress();

374:        if (address(producerToken) == address(0)) revert ZeroAddress();
```
[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
182:        if (_pxGmx == address(0)) revert ZeroAddress();
183:        if (_pxGlp == address(0)) revert ZeroAddress();
184:        if (_pirexFees == address(0)) revert ZeroAddress();
185:        if (_pirexRewards == address(0)) revert ZeroAddress();
186:        if (_delegateRegistry == address(0)) revert ZeroAddress();
187:        if (_gmxBaseReward == address(0)) revert ZeroAddress();
188:        if (_gmx == address(0)) revert ZeroAddress();
189:        if (_esGmx == address(0)) revert ZeroAddress();
190:        if (_gmxRewardRouterV2 == address(0)) revert ZeroAddress();
191:        if (_stakedGlp == address(0)) revert ZeroAddress();

317:         if (contractAddress == address(0)) revert ZeroAddress();

497:         if (token == address(0)) {

599:         if (token == address(0)) revert ZeroAddress();

727:         if (token == address(0)) revert ZeroAddress();

829:         if (token == address(0)) revert ZeroAddress();

885:         if (voteDelegate == address(0)) revert ZeroAddress();

926:         if (newContract == address(0)) revert ZeroAddress();

961:         if (oldContract == address(0)) revert ZeroAddress();
```
## Complementary Threshold Checks
Consider adding an optional threshold check for each zero amount check to avoid any input of edge value.

For instance, the following code line may be refactored as follows, catering for `6 <= _decimals <= 18`:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L33

```
        if (_decimals == 0 || _decimals < 6 || _decimals > 18) revert ZeroAmount(); 
```
All other instances entailed:

[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
105:        if (_poolFee == 0) revert ZeroAmount();

257:        if (fee == 0) revert InvalidParam();
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient); // @ f

63:    function setFeeRecipient(FeeRecipient f, address recipient) // @ f

69:        emit SetFeeRecipient(f, recipient); // @ f

71:        if (f == FeeRecipient.Treasury) { // @ f
```
[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
71:        UserState storage u = userRewardStates[user]; // @ u
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
159:        ProducerToken storage p = producerTokens[producerToken]; // @ p

285:        UserState storage u = producerTokens[producerToken].userStates[user]; // @ u

380:        ProducerToken storage p = producerTokens[producerToken]; // @ p
```
[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
95:    event SetFee(Fees indexed f, uint256 fee); // @ f
96:    event SetContract(Contracts indexed c, address contractAddress); // @ c

217:    function _computeAssetAmounts(Fees f, uint256 assets) // @ f

239:        RewardTracker r; // @ r

300:    function setFee(Fees f, uint256 fee) external onlyOwner { // @ f

313:    function setContract(Contracts c, address contractAddress) // @ c
```
## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value.

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
69:        emit SetFeeRecipient(f, recipient);

93:        emit SetTreasuryFeePercent(_treasuryFeePercent);
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
109:        emit PoolFeeUpdated(_poolFee);

121:        emit WithdrawalPenaltyUpdated(penalty);

133:        emit PlatformFeeUpdated(fee);

145:        emit CompoundIncentiveUpdated(incentive);

157:        emit PlatformUpdated(_platform);
```
[Fie: AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
99:        emit WithdrawalPenaltyUpdated(penalty);

111:        emit PlatformFeeUpdated(fee);

123:        emit CompoundIncentiveUpdated(incentive);

135:        emit PlatformUpdated(_platform);
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
98:        emit SetProducer(_producer);
```
[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
305:        emit SetFee(f, fee);

319:        emit SetContract(c, contractAddress);
```
## Use `delete` to Clear Variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic. 

For instance, the `a = 0` instance below should be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L118

```
            delete userRewardStates[msg.sender].rewards = 0;
```
All other instances entailed:

[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
391:            p.userStates[user].rewards = 0;
```
## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are the instances entailed:

[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
54:            (block.timestamp - globalState.lastUpdate) *

57:        globalState.lastUpdate = block.timestamp.safeCastTo32();

61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);

77:            (block.timestamp - u.lastUpdate);

79:        u.lastUpdate = block.timestamp.safeCastTo32();

83:        emit UserAccrue(user, block.timestamp, balance, rewards);
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
291:            (block.timestamp - u.lastUpdate);

293:        u.lastUpdate = block.timestamp.safeCastTo32();

297:        emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);

314:        if (block.timestamp != lastUpdate || totalSupply != lastSupply) {

316:                (block.timestamp - lastUpdate) *

325:                block.timestamp,
```
## Unnecessary Emission of `block.timestamp`
Any event emitted by Solidity includes the timestamp and block number implicitly. Adding it the second time just increases the cost of the transaction.

Here are the instances entailed:

[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);

83:        emit UserAccrue(user, block.timestamp, balance, rewards);
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
297:        emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);

323:            emit GlobalAccrue(
...
325:                block.timestamp,
```
## Assignment of Boolean in Conditional Check
Making a variable assignment in a conditional statement deviates from the standard use and intention of the check and can easily lead to confusion. 

Here is the instance entailed:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L355

```
        require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```
Consider moving the needed assignment before the conditional statement by having the code lines rewritten as follows.

```
        assets = previewRedeem(shares)) != 0;
        require(assets, "ZERO_ASSETS");
```
All other instances entailed:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L394-L398

```
        if (
            (shares = supply == 0
                ? assets
                : assets.mulDivDown(supply, totalAssets() - assets)) == 0
        ) revert ZeroShares();
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L311-L315

```
        if (
            (shares = supply == 0
                ? assets
                : assets.mulDivDown(supply, totalAssets() - assets)) == 0
        ) revert ZeroShares();
```
## Use of Hard-coded Addresses is Errors Prone
Each contract needs contract addresses in order to be integrated into other protocols and systems. The address below is currently hard-coded, which may cause errors and result in the codebase’s deployment with an incorrect asset. Using hard-coded values instead of deployer-provided values makes this contract incredibly difficult to test.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L18-L19

```
    IV3SwapRouter public constant SWAP_ROUTER =
        IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45);
```
Consider setting addresses when contracts are created rather than using hard-coded values. This practice will facilitate testing, making sure that contracts can be tested and reused across networks.

## Add a Timelock to Critical Parameter Change
It is a good practice to give time for users to react and adjust to critical changes with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. This allows users that do not accept the change to withdraw within the grace period. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious Owner making any malicious or ulterior intention). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

Consider extending the timelock feature beyond contract ownership management to business critical functions.

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
63:     function setFeeRecipient(FeeRecipient f, address recipient)

83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:    function setPlatformFee(uint256 fee) external onlyOwner {

140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:    function setPlatform(address _platform) external onlyOwner {
```
[Fie: AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
94:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

106:    function setPlatformFee(uint256 fee) external onlyOwner {

118:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

130:    function setPlatform(address _platform) external onlyOwner {
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
93:    function setProducer(address _producer) external onlyOwner {

151:    function addRewardToken(ERC20 producerToken, ERC20 rewardToken)

179:    function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
```
[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
300:    function setFee(Fees f, uint256 fee) external onlyOwner {

313:    function setContract(Contracts c, address contractAddress)
```
## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of an upgradeable contract just in case it would entail some child contracts in the future that might introduce new variables. Devoid of a storage gap addition, when the upgradable contract introduces new variables, it may override the variables in the inheriting contract, leading to storage collisions.

Adding the following line of code would ensure no shifting down of storage in the inheritance chain of the inheriting contracts:

```
    uint256[50] private __gap;
```
Here is the contract instance entailed:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

## Add a Constructor Initializer
As per Openzeppelin's recommendation:

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/6

The guidelines are now to prevent front-running of `initialize()` on an implementation contract, by adding an empty constructor with the initializer modifier. Hence, the implementation contract gets initialized atomically upon deployment.

This feature is readily incorporated in the Solidity Wizard since the UUPS vulnerability discovery. You would just need to check UPGRADEABILITY to have the following constructor code block added to the contract:

```
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() {
        _disableInitializers();
    }
```
Here is the contract instance with the missing constructor above:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to gas limitations or failed transactions. Consider bounding the loop length if the array is expected to be growing and/or handling a huge list of elements to avoid unnecessary gas wastage and denial of service.

[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
163:        for (uint256 i; i < len; ++i) {

351:        for (uint256 i; i < pLen; ++i) {

396:            for (uint256 i; i < rLen; ++i) {
```
## Use `require()` Instead of `assert()` to Validate Users` Input
The assert function should only be used to examine invariants and test for internal problems. When used correctly, it can assess your contract and discover the conditions and function calls that will result in a failed assert. A properly running program should never reach a failing assert statement; if this occurs, there is a flaw in your contract that has to be addressed. 

Here is one of the instances entailed which should be replaced by `require()` to validate a user's input:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225

```
        assert(feeAmount + postFeeAmount == assets);
```
On a side note, the `assert()` function when false, uses up all the remaining gas and reverts all the changes made. On the other hand, a `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.

Please visit the following link for further details:

https://codedamn.com/news/solidity/assert-vs-require-in-solidity

## Empty Event
The following event has no parameter in it to emit anything. Consider refactoring or removing these unusable lines of code.

[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
144:    event ClearVoteDelegate();

896:        emit ClearVoteDelegate();
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances missing NatSpec in its entirety:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IPirexRewards.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IProducer.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IAutoPxGlp.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol