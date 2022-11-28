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