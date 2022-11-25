## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Using Private Rather Than Public For Constants, Saves Gas | 16 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | Empty Blocks Should Be Removed Or Emit Something | 2 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | `require()` Should Be Used Instead Of `assert()` | 1 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | `<x> += <y>` Costs M ore Gas Than `<x> = <x> + <y>` For State Variables | 6 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 3 | 105 |
| [GAS&#x2011;6](#GAS&#x2011;6) | Public Functions To External | 19 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 10 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Superfluous event fields | 7 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | State variables only set in the `constructor` should be declared immutable | 1 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | `internal` functions only called once can be inlined to save gas | 7 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | Variables can be packed into fewer storage slots | 4 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Setting the `constructor` to `payable` | 8 | 104 |
| [GAS&#x2011;13](#GAS&#x2011;13) | Functions guaranteed to revert when called by normal users can be marked `payable` | 32 | 672 |



Total: 116 contexts over 13 issues

## Gas Optimizations


### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Using Private Rather Than Public For Constants, Saves Gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

#### <ins>Proof Of Concept</ins>


```solidity
20: uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
23: uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L20

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L23



```solidity
44: uint256 public constant FEE_DENOMINATOR = 1_000_000;
47: uint256 public constant FEE_MAX = 200_000;

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L44

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L47



```solidity
9: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
10: bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L9-L10

```solidity
18: uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
19: uint256 public constant MAX_PLATFORM_FEE = 2000;
20: uint256 public constant FEE_DENOMINATOR = 10000;
21: uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
22: uint256 public constant EXPANDED_DECIMALS = 1e30;

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L18-L22

```solidity
18: IV3SwapRouter public constant SWAP_ROUTER =
20: uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
21: uint256 public constant MAX_PLATFORM_FEE = 2000;
22: uint256 public constant FEE_DENOMINATOR = 10000;
23: uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L18-L23



#### <ins>Recommended Mitigation Steps</ins>

Set variable to private.



### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
12: {}
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxGmx.sol#L12

```solidity
23: {}
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxGmx.sol#L23




### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```solidity
225: assert(feeAmount + postFeeAmount == assets);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L225





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> `<x> += <y>`/`<x> -= <y>` Costs More Gas Than `<x> = <x> + <y>`/`<x> = <x> - <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
361: producerState.rewardStates[rewardTokens[i]] += r;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L361

```solidity
85: balanceOf[msg.sender] -= amount;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L85

```solidity
90: balanceOf[to] += amount;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L90

```solidity
119: balanceOf[from] -= amount;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L119

```solidity
124: balanceOf[to] += amount;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L124

```solidity
95: rewardState += rewardAmount;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L95





### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
163: for (uint256 i; i < len; ++i) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L163

```solidity
351: for (uint256 i; i < pLen; ++i) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L351

```solidity
396: for (uint256 i; i < rLen; ++i) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L396




### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function clearVoteDelegate() public onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L895

```solidity
function initialize() public initializer {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L85

```solidity
function userAccrue(ERC20 producerToken, address user) public {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L281

```solidity
function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public override returns (bool) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L109

```solidity
function totalAssets() public view override returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L142

```solidity
function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public override returns (uint256 shares) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L436

```solidity
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public override returns (uint256 assets) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L449

```solidity
function totalAssets() public view override returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L164

```solidity
function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public override returns (uint256 shares) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L315

```solidity
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public override returns (uint256 assets) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L339

```solidity
function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public virtual returns (uint256 shares) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L99

```solidity
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public virtual returns (uint256 assets) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L124

```solidity
function totalAssets() public view virtual returns (uint256);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L154

```solidity
function previewMint(uint256 shares) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L187

```solidity
function maxDeposit(address) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L217

```solidity
function maxMint(address) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L221

```solidity
function maxWithdraw(address owner) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L225

```solidity
function maxRedeem(address owner) public view virtual returns (uint256) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L229

```solidity
function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public override returns (bool) {
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L256






### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
11: uint32 lastUpdate;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/Common.sol#L11

```solidity
6: uint224 lastSupply;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/Common.sol#L6

```solidity
12: uint224 lastBalance;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/Common.sol#L12

```solidity
20: uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L20

```solidity
23: uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L23

```solidity
28: uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L28

```solidity
26: uint24 public poolFee = 3000;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L26





### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Superfluous event fields

`block.number` and `block.timestamp` are added to the event information by default, so adding them manually will waste additional gas.

#### <ins>Proof Of Concept</ins>


```solidity
96: event SetContract(Contracts indexed c, address contractAddress);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L96

```solidity
50: event GlobalAccrue(
        ERC20 indexed producerToken,
        uint256 lastUpdate,
        uint256 lastSupply,
        uint256 rewards
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L50

```solidity
63: event Harvest(
        ERC20[] producerTokens,
        ERC20[] rewardTokens,
        uint256[] rewardAmounts
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L63

```solidity
27: event Deposit(
        address indexed caller,
        address indexed owner,
        uint256 assets,
        uint256 shares
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L27

```solidity
34: event Withdraw(
        address indexed caller,
        address indexed receiver,
        address indexed owner,
        uint256 assets,
        uint256 shares
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PirexERC4626.sol#L34

```solidity
21: event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L21

```solidity
22: event UserAccrue(
        address indexed user,
        uint256 lastUpdate,
        uint256 lastSupply,
        uint256 rewards
    );
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L22





### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> State variables only set in the `constructor` should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

#### <ins>Proof Of Concept</ins>

```solidity
43: pxGmx = ERC20(_pxGmx)
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L43





### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
474: function afterDeposit

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L474

```solidity
487: function afterWithdraw

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L487

```solidity
501: function afterTransfer

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L501

```solidity
222: function beforeDeposit

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L222

```solidity
49: function _globalAccrue

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L49

```solidity
68: function _userAccrue

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L68

```solidity
90: function _harvest

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/PxGmxReward.sol#L90


### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Variables can be packed into fewer storage slots

Variables can be packed with lower values to be more gas efficient. If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper.
Estimated storage slots that can be saved: 15


#### <ins>Proof Of Concept</ins>

```solidity
18:	   uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
19:    uint256 public constant MAX_PLATFORM_FEE = 2000;
20:    uint256 public constant FEE_DENOMINATOR = 10000;
21:    uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
22:    uint256 public constant EXPANDED_DECIMALS = 1e30;

24:    uint256 public withdrawalPenalty = 300;
25:    uint256 public platformFee = 1000;
26:    uint256 public compoundIncentive = 1000;
27:    address public platform;

30:    address public immutable rewardsModule;

```

Can save up to 6 storage slots by changing to:

```solidity
	uint64 public constant MAX_WITHDRAWAL_PENALTY = 500;
    uint64 public constant MAX_PLATFORM_FEE = 2000;
    uint64 public constant FEE_DENOMINATOR = 10000;
    uint64 public constant MAX_COMPOUND_INCENTIVE = 5000;
    uint256 public constant EXPANDED_DECIMALS = 1e30;

    uint64 public withdrawalPenalty = 300;
    uint64 public platformFee = 1000;
    address public platform;
    uint64 public compoundIncentive = 1000;
    address public immutable rewardsModule;

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L18-L30


```solidity    
20:  uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
21:  uint256 public constant MAX_PLATFORM_FEE = 2000;
22:  uint256 public constant FEE_DENOMINATOR = 10000;
23:  uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

26:  uint24 public poolFee = 3000;

28:  uint256 public withdrawalPenalty = 300;
29:  uint256 public platformFee = 1000;
30:  uint256 public compoundIncentive = 1000;
31:  address public platform;

34:  address public immutable rewardsModule;
```

Can save up to 7 storage slots (Technically can save even 8 storage slots with reduced uint size but preferred to keep it at most to uint32/uint64) by changing to:

```solidity    
    uint64 public constant MAX_WITHDRAWAL_PENALTY = 500;
    uint64 public constant MAX_PLATFORM_FEE = 2000;
    uint64 public constant FEE_DENOMINATOR = 10000;
    uint64 public constant MAX_COMPOUND_INCENTIVE = 5000;

    uint24 public poolFee = 3000;
    address public platform;

    uint32 public withdrawalPenalty = 300;
    uint32 public platformFee = 1000;
    uint32 public compoundIncentive = 1000;

    address public immutable rewardsModule;
```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L20-L34

```solidity  
44:  uint256 public constant FEE_DENOMINATOR = 1_000_000;
47:  uint256 public constant FEE_MAX = 200_000;
```

Can save up to 1 storage slot by changing to:

```solidity  
44:  uint128 public constant FEE_DENOMINATOR = 1_000_000;
47:  uint128 public constant FEE_MAX = 200_000;
```
https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L44-L47

```solidity    
20:  uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
23:  uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
28:  uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
31:  address public treasury;
32:  address public contributors;
```

Can save up to 1 storage slot by changing to:

```solidity    
	uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
	address public treasury;
	uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
	address public contributors;
	uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
```
https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L20-L32


### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

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



### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

#### <ins>Proof Of Concept</ins>

```solidity
63: function setFeeRecipient(FeeRecipient f, address recipient)
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L63

```solidity
83: function setTreasuryFeePercent(uint8 _treasuryFeePercent)
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexFees.sol#L83

```solidity
272: function configureGmxState() external onlyOwner whenPaused {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L272

```solidity
300: function setFee(Fees f, uint256 fee) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L300

```solidity
313: function setContract(Contracts c, address contractAddress)
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L313

```solidity
739: function claimRewards()
        external
        onlyPirexRewards
        returns (
            ERC20[] memory producerTokens,
            ERC20[] memory rewardTokens,
            uint256[] memory rewardAmounts
        )
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L739

```solidity
824: function claimUserReward(
        address token,
        uint256 amount,
        address receiver
    ) external onlyPirexRewards {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L824

```solidity
862: function setDelegationSpace(
        string memory _delegationSpace,
        bool shouldClear
    ) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L862

```solidity
884: function setVoteDelegate(address voteDelegate) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L884

```solidity
895: function clearVoteDelegate() public onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L895

```solidity
909: function setPauseState(bool state) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L909

```solidity
921: function initiateMigration(address newContract)
        external
        whenPaused
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L921

```solidity
956: function completeMigration(address oldContract)
        external
        whenPaused
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexGmx.sol#L956

```solidity
93: function setProducer(address _producer) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L93

```solidity
151: function addRewardToken(ERC20 producerToken, ERC20 rewardToken)
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L151

```solidity
179: function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L179

```solidity
432: function setRewardRecipientPrivileged(
        address lpContract,
        ERC20 producerToken,
        ERC20 rewardToken,
        address recipient
    ) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L432

```solidity
461: function unsetRewardRecipientPrivileged(
        address lpContract,
        ERC20 producerToken,
        ERC20 rewardToken
    ) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PirexRewards.sol#L461

```solidity
45: function mint(address to, uint256 amount)
        external
        virtual
        onlyRole(MINTER_ROLE)
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L45

```solidity
62: function burn(address from, uint256 amount)
        external
        virtual
        onlyRole(BURNER_ROLE)
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxERC20.sol#L62

```solidity
19: function burn(address from, uint256 amount)
        external
        override
        onlyRole(BURNER_ROLE)
    {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/PxGmx.sol#L19

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
106: function setPlatform(address _platform) external onlyOwner {
130: function setPlatform(address _platform) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGlp.sol#L106

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
128: function setPlatform(address _platform) external onlyOwner {
152: function setPlatform(address _platform) external onlyOwner {

```

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L128

https://github.com/code-423n4/2022-11-redactedcartel/tree/main/src/vaults/AutoPxGmx.sol#L152





#### <ins>Recommended Mitigation Steps</ins>
Functions guaranteed to revert when called by normal users can be marked payable.