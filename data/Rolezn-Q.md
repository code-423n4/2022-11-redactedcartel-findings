## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | `decimals()` not part of ERC20 standard | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Contracts are not using their OZ Upgradeable counterparts | 2 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing parameter validation in `constructor` | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | The `nonReentrant` modifier should occur before all other modifiers | 6 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Owner can renounce Ownership | 1 |
| [LOW&#x2011;6](#LOW&#x2011;6) | `require()` should be used instead of `assert()` | 1 |
| [LOW&#x2011;7](#LOW&#x2011;7) | `whenPaused` modifier should apply to additional functions | 1 |


Total: 13 contexts over 7 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 19 |
| [NC&#x2011;2](#NC&#x2011;2) | Use a more recent version of Solidity | 1 |
| [NC&#x2011;3](#NC&#x2011;3) | Event Is Missing Indexed Fields | 5 |
| [NC&#x2011;4](#NC&#x2011;4) | Implementation contract may not be initialized | 8 |
| [NC&#x2011;5](#NC&#x2011;5) | Use Underscores for Number Literals | 2 |
| [NC&#x2011;6](#NC&#x2011;6) | Use of Block.Timestamp | 5 |
| [NC&#x2011;7](#NC&#x2011;7) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 2 |
| [NC&#x2011;8](#NC&#x2011;8) | `emit` should happen after `clearDelegate` and `setVoteDelegate` | 2 |
| [NC&#x2011;9](#NC&#x2011;9) | `block.timestamp` is already used when emitting events, no need to input timestamp | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Event emit should emit a parameter | 1 |

Total: 46 contexts over 10 issues

## Low Risk Issues


### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

#### <ins>Proof Of Concept</ins>


```solidity
48: constructor(
49:      ERC20 _asset,
50:      string memory _name,
51:      string memory _symbol
52:  ) ERC20(_name, _symbol, _asset.decimals()) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#LL48-52





### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
6: import {Pausable} from "openzeppelin-contracts/contracts/security/Pausable.sol";

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L4

```solidity
5: import {AccessControl} from "openzeppelin-contracts/contracts/access/AccessControl.sol";

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L5



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `openzeppelin-contracts-upgradeable` instead of `openzeppelin-contracts`.



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
69: address _pxGmx
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L69



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> The `nonReentrant` modifier should occur before all other modifiers

Currently the `nonReentrant` modifier is not the first to occur, it should occur before all other modifiers.

#### <ins>Proof Of Concept</ins>


```solidity
378: function depositGmx(uint256 amount, address receiver)
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L378

```solidity
422: function depositFsGlp(uint256 amount, address receiver)
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L422

```solidity
554: function depositGlpETH(
        uint256 minUsdg,
        uint256 minGlp,
        address receiver
    )
        external
        payable
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L554

```solidity
583: function depositGlp(
        address token,
        uint256 tokenAmount,
        uint256 minUsdg,
        uint256 minGlp,
        address receiver
    )
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L583

```solidity
685: function redeemPxGlpETH(
        uint256 amount,
        uint256 minOut,
        address receiver
    )
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L685

```solidity
712: function redeemPxGlp(
        address token,
        uint256 amount,
        uint256 minOut,
        address receiver
    )
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {
```


#### <ins>Recommended Mitigation Steps</ins>

Re-sort modifiers so that the `nonReentrant` modifier occurs first.


### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Owner can renounce Ownership

Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.
`PirexRewards.sol` contract inherits the `OwnableUpgradeable` contract in this project which implements the `renounceOwnership()` call. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

#### <ins>Proof Of Concept</ins>

```solidity
15: contract PirexRewards is OwnableUpgradeable {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L15

#### <ins>Recommended Mitigation Steps</ins>

It is recommended to override the function an disable the option to renounce if its not needed.



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> `require()` Should Be Used Instead Of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its <a href="https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require">documentation</a> states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

#### <ins>Proof Of Concept</ins>


```solidity
225: assert(feeAmount + postFeeAmount == assets);

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L225

### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> `whenPaused` modifier should apply to additional functions

In `PirexGmx.sol` contract the `whenPaused` modifier is applied to states changes such as the function `configureGmxState`.
As a result, the `whenPaused` modifier should also apply to other similar functions.

#### Proof of Concept
`whenPaused` modifier should apply to following functions as well:
```solidity
function setContract(Contracts c, address contractAddress)
```
https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L313


## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
63: function setFeeRecipient(FeeRecipient f, address recipient)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L63

```solidity
83: function setTreasuryFeePercent(uint8 _treasuryFeePercent)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L83

```solidity
300: function setFee(Fees f, uint256 fee) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L300

```solidity
313: function setContract(Contracts c, address contractAddress)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L313

```solidity
862: function setDelegationSpace(
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L862

```solidity
884: function setVoteDelegate(address voteDelegate) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L884

```solidity
909: function setPauseState(bool state) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L909

```solidity
93: function setProducer(address _producer) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L93

```solidity
107: function setRewardRecipient(
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L107

```solidity
432: function setRewardRecipientPrivileged(
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L432

```solidity
94: function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L94

```solidity
106: function setPlatformFee(uint256 fee) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L106

```solidity
118: function setCompoundIncentive(uint256 incentive) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L118

```solidity
130: function setPlatform(address _platform) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L130

```solidity
104: function setPoolFee(uint24 _poolFee) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L104

```solidity
116: function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L116

```solidity
128: function setPlatformFee(uint256 fee) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L128

```solidity
140: function setCompoundIncentive(uint256 incentive) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L140

```solidity
152: function setPlatform(address _platform) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L152



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L2



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```solidity
event SetFeeRecipient(FeeRecipient f, address recipient);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L34

```solidity
event ClaimRewards(
        uint256 baseRewards,
        uint256 esGmxRewards,
        uint256 gmxBaseRewards,
        uint256 glpBaseRewards,
        uint256 gmxEsGmxRewards,
        uint256 glpEsGmxRewards
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L125

```solidity
event SetDelegationSpace(string delegationSpace, bool shouldClear);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L142

```solidity
event Harvest(
        ERC20[] producerTokens,
        ERC20[] rewardTokens,
        uint256[] rewardAmounts
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L63

```solidity
event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L21







### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>

 
```solidity
50: constructor(address _treasury, address _contributors) Owned(msg.sender)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L50

```solidity
167: constructor(
        address _pxGmx,
        address _pxGlp,
        address _pirexFees,
        address _pirexRewards,
        address _delegateRegistry,
        address _gmxBaseReward,
        address _gmx,
        address _esGmx,
        address _gmxRewardRouterV2,
        address _stakedGlp
    ) Owned(msg.sender)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L167

```solidity
24: constructor(
        address _pirexRewards,
        string memory _name,
        string memory _symbol,
        uint8 _decimals
    ) ERC20(_name, _symbol, _decimals)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L24

```solidity
10: constructor(address _pirexRewards)
        PxERC20(_pirexRewards, "Pirex GMX", "pxGMX", 18)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxGmx.sol#L10

```solidity
66: constructor(
        address _gmxBaseReward,
        address _asset,
        address _pxGmx,
        string memory _name,
        string memory _symbol,
        address _platform,
        address _rewardsModule
    ) PxGmxReward(_pxGmx) PirexERC4626(ERC20(_asset), _name, _symbol)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L66

```solidity
73: constructor(
        address _gmxBaseReward,
        address _gmx,
        address _asset,
        string memory _name,
        string memory _symbol,
        address _platform,
        address _rewardsModule
    ) Owned(msg.sender) PirexERC4626(ERC20(_asset), _name, _symbol)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L73

```solidity
48: constructor(
        ERC20 _asset,
        string memory _name,
        string memory _symbol
    ) ERC20(_name, _symbol, _asset.decimals())
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L48

```solidity
40: constructor(address _pxGmx) Owned(msg.sender)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L40





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Use Underscores for Number Literals

There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

#### <ins>Proof Of Concept</ins>

```solidity
20: uint256 public constant FEE_DENOMINATOR = 10000;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L20

```solidity
22: uint256 public constant FEE_DENOMINATOR = 10000;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L22


#### <ins>Recommended Mitigation Steps</ins>
Use `10_000` instead of `10000` to improve readability.


### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
291: (block.timestamp - u.lastUpdate);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L291

```solidity
314: if (block.timestamp != lastUpdate || totalSupply != lastSupply) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L314

```solidity
316: (block.timestamp - lastUpdate) *
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L316

```solidity
54: (block.timestamp - globalState.lastUpdate) *
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L54

```solidity
77: (block.timestamp - u.lastUpdate);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L77



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```solidity
9: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L9

```solidity
10: bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L10


### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> `emit` should happen after `clearDelegate` and `setVoteDelegate`

Events should be emitted only after the relevant action has occurred and not before.

#### <ins>Proof Of Concept</ins>

```solidity
895: function clearVoteDelegate() public onlyOwner {
896:     emit ClearVoteDelegate();
897:
898:     delegateRegistry.clearDelegate(delegationSpace);
899: }
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L895-L899


```solidity
106: emit DistributeFees(
107:    token,
108:    distribution,
109:    treasuryDistribution,
110:    contributorsDistribution
111: );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L63

### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> `block.timestamp` is already used when emitting events, no need to input timestamp

#### <ins>Proof Of Concept</ins>

```solidity
297: emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);
```
https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L297

```solidity
61: emit GlobalAccrue(block.timestamp, totalSupply, rewards);
```
https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L61

```solidity
83: emit UserAccrue(user, block.timestamp, balance, rewards);
```
https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L83


### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Event emit should emit a parameter

Some emitted events do not have any emitted parameters. It is recommended to add some parameter such as state changes or value changes when events are emitted

#### <ins>Proof Of Concept</ins>


```solidity
896: emit ClearVoteDelegate()
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L896


