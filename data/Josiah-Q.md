## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logic that depends strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are the 12 instances found.

[PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
54:            (block.timestamp - globalState.lastUpdate) *

57:        globalState.lastUpdate = block.timestamp.safeCastTo32();

61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);

77:            (block.timestamp - u.lastUpdate);

79:        u.lastUpdate = block.timestamp.safeCastTo32();

83:        emit UserAccrue(user, block.timestamp, balance, rewards);
```
[PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
291:            (block.timestamp - u.lastUpdate);

293:        u.lastUpdate = block.timestamp.safeCastTo32();

297:        emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);

314:        if (block.timestamp != lastUpdate || totalSupply != lastSupply) {

316:                (block.timestamp - lastUpdate) *

325:                block.timestamp,
```
## UNUSABLE EVENT
The following event is empty in the parameter field making it incapable of emitting anything. It is recommended removing this unusable line of code.

Here is 1 instance found.

[PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
144:    event ClearVoteDelegate();

896:        emit ClearVoteDelegate();
```

## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are the 4 contract instances found.

[IPirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IPirexRewards.sol)
[IProducer.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IProducer.sol)
[IAutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IAutoPxGlp.sol)
[Common.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol)

## CODEHASH CHECKS
It is recommended adding an optional and complementary codehash check for immutable addresses at the constructor since the zero address check cannot guarantee a matching/correct input address.

Here are the 23 instances found.

[PirexFees.sol#L51-L52](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L51-L52)

```
51:        if (_treasury == address(0)) revert ZeroAddress();
52:        if (_contributors == address(0)) revert ZeroAddress();
```
[PxERC20.sol#L30](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L30)

```
30:        if (_pirexRewards == address(0)) revert ZeroAddress();
```
[PxGmxReward.sol#L41](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L41)

```
41:        if (_pxGmx == address(0)) revert ZeroAddress();
```
[AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
82:        if (_gmxBaseReward == address(0)) revert ZeroAddress();
83:        if (_gmx == address(0)) revert ZeroAddress();
84:        if (_asset == address(0)) revert ZeroAddress();

87:        if (_platform == address(0)) revert ZeroAddress();
88:        if (_rewardsModule == address(0)) revert ZeroAddress();
```
[AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
75:        if (_gmxBaseReward == address(0)) revert ZeroAddress();
76:        if (_asset == address(0)) revert ZeroAddress();

79:        if (_platform == address(0)) revert ZeroAddress();
80:        if (_rewardsModule == address(0)) revert ZeroAddress();
```
[PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

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
```
## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users of needed services. It is recommended setting a threshold for the array length if the array could grow to or entail an excessive size.

Here are the 3 instances found.

[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
163:        for (uint256 i; i < len; ++i) {

351:        for (uint256 i; i < pLen; ++i) {

396:            for (uint256 i; i < rLen; ++i) {
```
## TIMELOCK FOR CRITICAL PARAMETER CHANGE
It is a good practice giving time to users to react and adjust to critical changes with a mandatory time window between the changes. The first step is simply broadcasting to users with a specific change that is coming whilst the second step commits that change after an appropriate period of waiting. This would allow time for users opposing to the change to withdraw within the set time frame. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of the owner making a malicious act). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

Here are the 16 `setter` instances found.

[PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
63:     function setFeeRecipient(FeeRecipient f, address recipient)

83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)
```
[AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:    function setPlatformFee(uint256 fee) external onlyOwner {

140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:    function setPlatform(address _platform) external onlyOwner {
```
[AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
94:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

106:    function setPlatformFee(uint256 fee) external onlyOwner {

118:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

130:    function setPlatform(address _platform) external onlyOwner {
```
[PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
93:    function setProducer(address _producer) external onlyOwner {

151:    function addRewardToken(ERC20 producerToken, ERC20 rewardToken)

179:    function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
```
[PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
300:    function setFee(Fees f, uint256 fee) external onlyOwner {

313:    function setContract(Contracts c, address contractAddress)
```
## STORAGE GAP FOR UPGRADEABLE CONTRACTS
Consider adding a storage gap at the end of each upgradeable contract. In the event some contracts needed to inherit from them, there would not be an issue shifting down of storage in the inheritance chain. Generally, storage gaps are a novel way of reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. If not, the variable in the child contract might be overridden whenever new variables are added to it. This storage collision could have unintended and vulnerable consequences to the child contracts.

Here is 1 contract instances found.

[PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

## INCOMPLETE CONSTRUCTOR INITIALIZER
As stated in the Openzeppelin's forum below:

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/6

"The guidelines are now to make it impossible for anyone to run `initialize` on an implementation contract, by adding an empty constructor with the `initializer` modifier. So the implementation contract gets initialized automatically upon deployment."

This feature has since been updated in the Solidity Wizard following the UUPS vulnerability discovery. You would just need to check UPGRADEABILITY to have the following constructor code block added to the contract:

```
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() {
        _disableInitializers();
    }
```
Here is 1 contract instance with missing `_disableInitializers()`:

[PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

## EVENT PARAMETERS SHOULD BE INDEXED
Up to three event parameters should be indexed. This will help filter off the logs in listening for specifically wanted data. Using `indexed` has the benefit of making the arguments log topics instead of data.

Here are the 3 instances found.

[PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient);
35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
```
[PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
63:    event Harvest(
```
## COMPARISON CHECKS
When implementing doing a zero value check, it is recommended setting boundaries that would minimize edge value input. 

Here are the 3 instances found.

[PxERC20.sol#L33](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L33)

```
33:        if (_decimals == 0) revert ZeroAmount();
```
[AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
105:        if (_poolFee == 0) revert ZeroAmount();

257:        if (fee == 0) revert InvalidParam();
```
## BLOATED LOG POISONING
Public and external functions with emitted events could be spammed by malicious actors to create bloated logs at the back end if checks are insufficient to stem these malicious from transpiring. 

Here are the 3 instances found.

[PirexFees.sol#L100-L116](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L116)

It is recommended emitting the event only when `distribution != 0`:

```
    function distributeFees(ERC20 token) external {
        uint256 distribution = token.balanceOf(address(this));
        uint256 treasuryDistribution = (distribution * treasuryFeePercent) /
            FEE_PERCENT_DENOMINATOR;
        uint256 contributorsDistribution = distribution - treasuryDistribution;

        emit DistributeFees(
            token,
            distribution,
            treasuryDistribution,
            contributorsDistribution
        );

        // Favoring push over pull to reduce accounting complexity for different tokens
        token.safeTransfer(treasury, treasuryDistribution);
        token.safeTransfer(contributors, contributorsDistribution);
    }
```
[PirexRewards.sol#L314](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L314)

It is recommended replacing `||` with `&&`:

```
        if (block.timestamp != lastUpdate || totalSupply != lastSupply) {
```
[PirexRewards.sol#L281-L298](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L281-L298)

It is recommended implementing the refactored if block above to the following code block before calculating the reward amount:

```
    function userAccrue(ERC20 producerToken, address user) public {
        if (address(producerToken) == address(0)) revert ZeroAddress();
        if (user == address(0)) revert ZeroAddress();

        UserState storage u = producerTokens[producerToken].userStates[user];
        uint256 balance = producerToken.balanceOf(user);

        // Calculate the amount of rewards accrued by the user up to this call
        uint256 rewards = u.rewards +
            u.lastBalance *
            (block.timestamp - u.lastUpdate);

        u.lastUpdate = block.timestamp.safeCastTo32();
        u.lastBalance = balance.safeCastTo224();
        u.rewards = rewards;

        emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);
    }
```