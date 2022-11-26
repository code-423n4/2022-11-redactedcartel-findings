## Summary
### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Remove the `initializer` modifier [20 K Gas Saved] | 1 |
| [G-02] | Packing Storage | 4 |
| [G-03] | No need `assert` check in `_computeAssetAmounts()` [19 K  Gas Saved ] | 1 |
| [G-04] | Use ``assembly`` to write _address storage values_ | 27 |
| [G-05] | The solady Library's Ownable contract is significantly gas-optimized, which can be used | 2|
| [G-06] | Setting The Constructor To Payable  | 8 |
| [G-07] | Functions guaranteed to revert_ when callled by normal users can be marked `payable`   | 8 |
| [G-08] | Project file uses Transparent Proxy model but UUPS model is cheaper| 1 |
| [G-09] | Optimize names to save gas | All |

Total 9 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Use `v4.8.0 OpenZeppelin` contracts |

Total 1 suggestion
### [G-01] Remove the `initializer` modifier [20 K Gas Saved]

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

```solidity

src/PirexRewards.sol:
  84  
  85:     function initialize() public initializer {
  86:         __Ownable_init();
  87:     }
```


In the EVM, the constructor's job is actually to return the bytecode that will live at the contract's address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address! So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let's update our `initialize()` function to only run if we are inside a constructor:

```diff
src/PirexRewards.sol:
  84  
  85:     function initialize() public initializer {
+          require(address(this).code.length == 0, 'not in constructor');
  86:         __Ownable_init();
  87:     }

```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!

### [G-02] Packing Storage

**Context:**
[AutoPxGlp.sol#L24-L30](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L24-L30)

**Recommendation:**
Storing state variables packaging is more gas efficient.

_Current Code:_
```js
    uint256 public withdrawalPenalty = 300;
    uint256 public platformFee = 1000;
    uint256 public compoundIncentive = 1000;
    address public platform;
```
_Recommendation Code:_
```js
    address public platform;
    uint64 public withdrawalPenalty = 300;
    uint64 public platformFee = 1000;
    uint64 public compoundIncentive = 1000;
```

| Storage Variable | Slot | Offset | Length |
|:-------:|:-------:|:-------:|:-------:|
| platform |  0 | 0 | 20 |
| withdrawalPenalty  | 0 | 20 | 8 |
| platformFee | 1 | 0 | 8 |
| compoundIncentive | 1 | 8 | 8 |

### [G-03]  No need `assert` check in `_computeAssetAmounts()` [19 K  Gas Saved ]

The `assert` check in this function is not needed. Because this check will always be true because of this line: `postFeeAmount = assets - feeAmount;`
Deployment Gas Saved : 19 K
Transaction Gas Saved : 90 

**Context:**
```solidity
src/PirexGmx.sol:
  216       */
  217:     function _computeAssetAmounts(Fees f, uint256 assets)
  218:         internal
  219:         view
  220:         returns (uint256 postFeeAmount, uint256 feeAmount)
  221:     {
  222: 
  223: 
  224:         feeAmount = (assets * fees[f]) / FEE_DENOMINATOR;
  225:         postFeeAmount = assets - feeAmount;
  226: 
  227:         assert(feeAmount + postFeeAmount == assets);
  228:     }

```


**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;

    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();

    }
        function testGas() public {
            c0.depositGmx(5,200_000);
            c1.depositGmx(5,200_000);            
        }
}

contract Contract0 {

    uint256 constant FEE_DENOMINATOR = 1_000_000;

    function depositGmx(uint256 amount, uint256 f)  external {
        (uint256 postFeeAmount, uint256 feeAmount) = _computeAssetAmounts(f,amount);
    }

     function _computeAssetAmounts(uint f, uint256 assets) internal view returns (uint256 postFeeAmount, uint256 feeAmount) {
        feeAmount = (assets * f ) / FEE_DENOMINATOR;
        postFeeAmount = assets - feeAmount;

        assert(feeAmount + postFeeAmount == assets);
    }
}

contract Contract1 {

    uint256 constant FEE_DENOMINATOR = 1_000_000;

    function depositGmx(uint256 amount, uint256 f)  external {
        (uint256 postFeeAmount, uint256 feeAmount) = _computeAssetAmounts(f,amount);
    }

     function _computeAssetAmounts(uint f, uint256 assets) internal view returns (uint256 postFeeAmount, uint256 feeAmount) {
        feeAmount = (assets * f ) / FEE_DENOMINATOR;
        postFeeAmount = assets - feeAmount;

    }
} 

```
**Gas Report:**

```js
| src/test/test.sol:Contract0 contract |                 |     |        |     |         |
|--------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                      | Deployment Size |     |        |     |         |
| 102347                               | 543             |     |        |     |         |
| Function Name                        | min             | avg | median | max | # calls |
| depositGmx                           | 608             | 608 | 608    | 608 | 1       |


| src/test/test.sol:Contract1 contract |                 |     |        |     |         |
|--------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                      | Deployment Size |     |        |     |         |
| 83329                                | 448             |     |        |     |         |
| Function Name                        | min             | avg | median | max | # calls |
| depositGmx                           | 518             | 518 | 518    | 518 | 1       |

```



### [G-04] Use ``assembly`` to write _address storage values_ 


**Context:**

```solidity

src/PirexFees.sol:
   50:     constructor(address _treasury, address _contributors) Owned(msg.sender) {
  
  54:         treasury = _treasury;
  55:         contributors = _contributors;

src/PirexGmx.sol:
  167:     constructor(
  168:         address _pxGmx,
  169:         address _pxGlp,
  170:         address _pirexFees,
  171:         address _pirexRewards,
  172:         address _delegateRegistry,
  173:         address _gmxBaseReward,
  174:         address _gmx,
  175:         address _esGmx,
  176:         address _gmxRewardRouterV2,
  177:         address _stakedGlp

  193:         pxGmx = PxERC20(_pxGmx);
  194:         pxGlp = PxERC20(_pxGlp);
  195:         pirexFees = PirexFees(_pirexFees);
  196:         pirexRewards = _pirexRewards;
  197:         delegateRegistry = DelegateRegistry(_delegateRegistry);
  198:         gmxBaseReward = ERC20(_gmxBaseReward);
  199:         gmx = ERC20(_gmx);
  200:         esGmx = ERC20(_esGmx);
  201:         gmxRewardRouterV2 = IRewardRouterV2(_gmxRewardRouterV2);
  202:         stakedGlp = IStakedGlp(_stakedGlp);

src/PxERC20.sol:
  24:     constructor(
  25:         address _pirexRewards,
  
  35:         pirexRewards = PirexRewards(_pirexRewards);

src/vaults/AutoPxGlp.sol:
  66:     constructor(
  67:         address _gmxBaseReward,
  68:         address _asset,
  69:         address _pxGmx,
  70:         string memory _name,
  71:         string memory _symbol,
  72:         address _platform,
  73:         address _rewardsModule
  74:     ) PxGmxReward(_pxGmx) PirexERC4626(ERC20(_asset), _name, _symbol) {

  82:         gmxBaseReward = ERC20(_gmxBaseReward);
  83:         platform = _platform;
  84:         rewardsModule = _rewardsModule;

src/vaults/AutoPxGmx.sol:
  73:     constructor(
  74:         address _gmxBaseReward,
  75:         address _gmx,
  76:         address _asset,
  77:         string memory _name,
  78:         string memory _symbol,
  79:         address _platform,
  80:         address _rewardsModule
  81:     ) Owned(msg.sender) PirexERC4626(ERC20(_asset), _name, _symbol) {

  90:         gmxBaseReward = ERC20(_gmxBaseReward);
  91:         gmx = ERC20(_gmx);
  92:         platform = _platform;
  93:         rewardsModule = _rewardsModule;
  
src/vaults/PirexERC4626.sol:
  48:     constructor(
  49:         ERC20 _asset,

  53:         asset = _asset;

src/vaults/PxGmxReward.sol:
  40:     constructor(address _pxGmx) Owned(msg.sender) {
  43:         pxGmx = ERC20(_pxGmx);
```


**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTestFoundry is DSTest {
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
        c2.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
    }
}

contract Contract1  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        rewardToken = token_;
        reward = reward_;
    }
}

contract Contract2  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        assembly {
            sstore(rewardToken.slot, token_)
            sstore(reward.slot, reward_)           

        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44490           ┆ 44490 ┆ 44490  ┆ 44490 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 38087                                     ┆ 221             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44457           ┆ 44457 ┆ 44457  ┆ 44457 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```
### [G-05] The solady Library's Ownable contract is significantly gas-optimized, which can be used

**Description:**
The project uses the `onlyOwner` authorization model I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol

### [G-06] Setting The Constructor To Payable [13 gas per instance]


**Description:**
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity

8 results

src/PirexFees.sol:
  50:     constructor(address _treasury, address _contributors) Owned(msg.sender) {

src/PirexGmx.sol:
  167:     constructor(

src/PxERC20.sol:
  24:     constructor(

src/PxGmx.sol:
  10:     constructor(address _pirexRewards)


src/vaults/AutoPxGlp.sol:
  66:     constructor(

src/vaults/AutoPxGmx.sol:
  73:     constructor(

src/vaults/PirexERC4626.sol:
  48:     constructor(

src/vaults/PxGmxReward.sol:
  40:     constructor(address _pxGmx) Owned(msg.sender) {

```

**Recommendation:**
Set the constructor to ```payable```

**Proof Of Concept:**
https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5?u=pcaversaccio

The optimizer was turned on and set to 10000 runs

```js
contract GasTestFoundry is DSTest {
    
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.x();
        c2.x();
    }
}

contract Contract1 {
    
    uint256 public dummy;
    constructor() payable {
        dummy = 1;
    }
    
    function x() public {

    }
}

contract Contract2 {
    
    uint256 public dummy;
    constructor() {
        dummy = 1;
    }
    
    function x() public {
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49563                                     ┆ 159             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤

╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49587                                     ┆ 172             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```
### [G-07]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

**Context:** 

```solidity
18 results - 5 files

src/PirexFees.sol:

  63:     function setFeeRecipient(FeeRecipient f, address recipient)
  64          external
  65          onlyOwner

  
  83:     function setTreasuryFeePercent(uint8 _treasuryFeePercent)
  84          external
  85          onlyOwner

src/PirexGmx.sol:

  300:     function setFee(Fees f, uint256 fee) external onlyOwner {
  301          if (fee > FEE_MAX) revert InvalidFee();

 
  313:     function setContract(Contracts c, address contractAddress)
  314          external
  315          onlyOwner

 
  862:     function setDelegationSpace(
  863          string memory _delegationSpace,
  864      bool shouldClear
  865      ) external onlyOwner {


  884:     function setVoteDelegate(address voteDelegate) external onlyOwner {

  909:     function setPauseState(bool state) external onlyOwner {
  

src/PirexRewards.sol:
   
   93:     function setProducer(address _producer) external onlyOwner {
 

  432:     function setRewardRecipientPrivileged(
  433          address lpContract,
  434        ERC20 producerToken,
  435      ERC20 rewardToken,
  436      address recipient
  437      ) external onlyOwner {


src/vaults/AutoPxGlp.sol:
   
   94:     function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
   
  106:     function setPlatformFee(uint256 fee) external onlyOwner {

  118:     function setCompoundIncentive(uint256 incentive) external onlyOwner {

  130:     function setPlatform(address _platform) external onlyOwner {


src/vaults/AutoPxGmx.sol:
 
  104:     function setPoolFee(uint24 _poolFee) external onlyOwner {

  116:     function setWithdrawalPenalty(uint256 penalty) external onlyOwner {
  
  128:     function setPlatformFee(uint256 fee) external onlyOwner {

  140:     function setCompoundIncentive(uint256 incentive) external onlyOwner {

  152:     function setPlatform(address _platform) external onlyOwner {
```

**Description:**
If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyowner or admin``` functions)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0.foo();
        c1.foo();
    }
}

contract Contract0 {
    uint256inverse;
    
    function foo() external {
       inverse++;
    }
}

contract Contract1 {
    uint256inverse;
    
    function foo() external payable {
       inverse++;
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 44293                                     ┆ 252             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                       ┆ 22308           ┆ 22308 ┆ 22308  ┆ 22308 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 41893                                     ┆ 240             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                       ┆ 22284           ┆ 22284 ┆ 22284  ┆ 22284 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```
### [G-08] Project file uses Transparent Proxy model but UUPS model is cheaper


UUPS proxies are a lot simpler than Transparent, and they only have the logic for delegating the call. This makes them much cheaper to deploy, and each all has half the overhead (just a single SLOAD).

https://twitter.com/smpalladino/status/1389939166109212675?s=20&t=Nd7ssD9sC_BNTtQF8lnxNg

### [G-09] Optimize names to save gas [22 gas per instance]

**Context:** 
All Contracts

**Description:** 
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` PirexGmx.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

PirexGmx.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
57508589  =>  _calculateRewards(bool,bool)
59524400  =>  _computeAssetAmounts(Fees,uint256)
74874323  =>  setVoteDelegate(address)
492b669d  =>  configureGmxState()
20587afb  =>  setFee(Fees,uint256)
dae84c59  =>  setContract(Contracts,address)
437a8d0a  =>  depositGmx(uint256,address)
f83e36e6  =>  depositFsGlp(uint256,address)
d21fe1ee  =>  _depositGlp(address,uint256,uint256,uint256,address)
f64d0094  =>  depositGlpETH(uint256,uint256,address)
c2ae96ef  =>  depositGlp(address,uint256,uint256,uint256,address)
a7fe1a7f  =>  _redeemPxGlp(address,uint256,uint256,address)
6151f1b7  =>  redeemPxGlpETH(uint256,uint256,address)
414cc4ce  =>  redeemPxGlp(address,uint256,uint256,address)
372500ab  =>  claimRewards()
3e9f3619  =>  claimUserReward(address,uint256,address)
1d3c2b2f  =>  setDelegationSpace(string,bool)
edf187f0  =>  clearVoteDelegate()
cdb88ad1  =>  setPauseState(bool)
71726c92  =>  initiateMigration(address)
24fcd907  =>  migrateReward()
6ac844be  =>  completeMigration(address)
```

### [S-01] Use `v4.8.0 OpenZeppelin` contracts

**Description:**
The upcoming v4.8.0 version of OpenZeppelin provides many small gas optimizations.

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0-rc.0

```js
v4.8.0-rc.0
⛽ Many small optimizations
```
