## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| PirexERC4626's implmentation is not fully up to EIP-4626's specification| 1 |
|[L-02]| initialize() function can be called by anybody | 1 |
|[L-03]| Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists | 19 |
|[L-04]| Use `Ownable2StepUpgradeable` instead of ` OwnableUpgradeable ` contract| 1 |
|[L-05]| Owner can renounce Ownership| 1 |
|[L-06]| Critical Address Changes Should Use Two-step Procedure | 4 |
|[L-07]|`DepositGlp` Event arguments names are confusing | 2 |
|[L-08]| Loss of precision due to rounding| 1 |
|[L-09]| First value check of argument of type enum in setFee function is missing| 1 |
|[L-10]| Hardcode the address causes no future updates| 1 |
|[L-11]| Lack of Input Validation | 6 |

Total 11 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01] |Insufficient coverage|1|
| [N-02] |Not using the named return variables anywhere in the function is confusing  |1 |
| [N-03] |Same Constant redefined elsewhere| 4 |
| [N-04] |Omissions in Events| 8 | 
| [N-05] |Add parameter to Event-Emit | 1 |
| [N-06] |NatSpec is missing  | 1 |
| [N-07] |Use `require` instead of `assert` | 1 |
| [N-08] |Implement some type of version counter that will be incremented automatically for contract upgrades | 1 |
| [N-09] |Constant values such as a call to keccak256(), should used to immutable rather than constant | 2 |
| [N-10] |For functions, follow Solidity standard naming conventions | 4  |
| [N-11] |Mark visibility of initialize(...) functions as ``external``| 1 |
| [N-12] |No same value input control | 8 |
| [N-13] |Include ``return parameters`` in _NatSpec comments_ | All |
| [N-14] |`0 address` check for ` asset ` | 1 |
| [N-15] |Use a single file for all system-wide constants| 6 |
| [N-16] |`Function writing` that does not comply with the `Solidity Style Guide`| All |
| [N-17] |Missing Upgrade Path for `PirexRewards` Implementation| 1 |
| [N-18] | No need `assert` check in `_computeAssetAmounts()` | 1 |

Total 18 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |
| [S-02] |Add NatSpec comments to the variables defined in Storage |

Total 2 suggestions


### [L-01] PirexERC4626's implmentation is not fully up to EIP-4626's specification


Must  return the maximum amount of shares mint would allow to be deposited to receiver and not cause a revert, which must not be higher than the actual maximum that would be accepted (it should underestimate if necessary). 

This assumes that the user has infinite assets, i.e. must not rely on `balanceOf` of asset.

```solidity

src/vaults/PirexERC4626.sol:
  216  
  217:     function maxDeposit(address) public view virtual returns (uint256) {
  218:         return type(uint256).max;
  219:     }
  220: 
  221:     function maxMint(address) public view virtual returns (uint256) {
  222:         return type(uint256).max;
  223:     }

```

Could cause unexpected behavior in the future due to non-compliance with EIP-4626 standard.

Similar problem for Sentimentxyz ;
https://github.com/sentimentxyz/protocol/pull/235/files


### Recommended Mitigation Steps

maxMint() and maxDeposit() should reflect the limitation of maxSupply.

Consider changing maxMint() and maxDeposit() to:



```solidity

function maxMint(address) public view virtual returns (uint256) {
    if (totalSupply >= maxSupply) {
        return 0;
    }
    return maxSupply - totalSupply;
}
function maxDeposit(address) public view virtual returns (uint256) {
    return convertToAssets(maxMint(address(0)));
}


```
### [L-02] initialize() function can be called by anybody

`initialize()` function can be called anybody when the contract is not initialized.

More importantly, if someone else runs this function, they will have full authority because of the `__Ownable_init()` function.

Here is a definition of `initialize()` function.

```solidity

src/PirexRewards.sol:
  84  
  85:     function initialize() public initializer {
  86:         __Ownable_init();
  87:     }
```


### Recommended Mitigation Steps

Add a control that makes `initialize()` only call the Deployer Contract or EOA;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```
### [L-03] Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists

Solmate's SafeTransferLib, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist (yet).

This is stated in the Solmate library:
https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9



```solidity

19 results 

src/PirexFees.sol:
    5: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";

  114:         token.safeTransfer(treasury, treasuryDistribution);
  115:         token.safeTransfer(contributors, contributorsDistribution);

src/PirexGmx.sol:
    7: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";

  392:         gmx.safeTransferFrom(msg.sender, address(this), amount);

  506:             t.safeTransferFrom(msg.sender, address(this), tokenAmount);

  643:             ERC20(pxGlp).safeTransferFrom(

  844:             gmxBaseReward.safeTransfer(receiver, postFeeAmount);
 
  847:                 gmxBaseReward.safeTransfer(address(pirexFees), feeAmount);

  946:         gmxBaseReward.safeTransfer(



src/vaults/AutoPxGlp.sol:
    6: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";


  258:                 asset.safeTransfer(msg.sender, pxGlpIncentive);
  259  
  260:             asset.safeTransfer(owner, totalPxGlpFee - pxGlpIncentive);

  274:                 pxGmx.safeTransfer(msg.sender, pxGmxIncentive);
  275  
  276:             pxGmx.safeTransfer(owner, totalPxGmxFee - pxGmxIncentive);

  344:         stakedGlp.safeTransferFrom(msg.sender, address(this), amount);

  387:         erc20Token.safeTransferFrom(msg.sender, address(this), tokenAmount);
  388  

src/vaults/AutoPxGmx.sol:
    7: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
	
  297:             if (incentive != 0) asset.safeTransfer(msg.sender, incentive);
  298  
  299:             asset.safeTransfer(owner, totalFee - incentive);

  336:         asset.safeTransfer(receiver, assets);

  361:         asset.safeTransfer(receiver, assets);

  382:         gmx.safeTransferFrom(msg.sender, address(this), amount);
```


### Recommended Mitigation Steps

Add a contract exist control in functions;
```js
pragma solidity >=0.8.0;

function isContract(address _addr) private returns (bool isContract) {
    isContract = _addr.code.length > 0;
}

```
### [L-04] Use `Ownable2StepUpgradeable` instead of ` OwnableUpgradeable ` contract

**Context:**
[PirexRewards.sol#L4](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L4)

**Description:**
```transferOwnership``` function is used to change Ownership from ```OwnableUpgradeable.sol```.

There is another Openzeppelin Ownable contract (Ownable2StepUpgradeable.sol) has  ` transferOwnership` function ,  use it is more secure due to 2-stage ownership transfer.

[Ownable2StepUpgradeable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/access/Ownable2StepUpgradeable.sol)

### [L-05] Owner can renounce Ownership

**Context:**
[PirexRewards.sol#L4](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L4)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Openzeppelin’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

`onlyOwner` functions;
```js
src/PirexRewards.sol:
  93:     function setProducer(address _producer) external onlyOwner {
151:     function addRewardToken(ERC20 producerToken, ERC20 rewardToken)  external onlyOwner

```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-06] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```solidity
src/PirexFees.sol:
  63:     function setFeeRecipient(FeeRecipient f, address recipient)

src/PirexGmx.sol:
  313:     function setContract(Contracts c, address contractAddress)
  884:     function setVoteDelegate(address voteDelegate) external onlyOwner {

src/external/DelegateRegistry.sol:
  18:     function setDelegate(bytes32 id, address delegate) public {

```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [L-07] `DepositGlp` Event arguments names are confusing


The `uint256 amount` argument of the `depositFsGlp` function in the PirexGmx.sol contract is named `deposited` in the `event DepositGlp` parameter. In `emit DepositGlp` this causes confusion in terms of user, code readability and web pages.

```solidity
src/PirexGmx.sol:
  421       */
  422:     function depositFsGlp(uint256 amount, address receiver)
  423:         external
  424:         whenNotPaused
  425:         nonReentrant
  426:         returns (
  427:             uint256,
  428:             uint256,
  429:             uint256
  430:         )

 452:         emit DepositGlp(
  453:             msg.sender,          	// caller
  454:             receiver,          		// receiver
  455:             address(stakedGlp),  	// token
  456:             0,                   		// tokenAmount
  457:             0,                  		 // minUsdg
  458:             0,                   		// minGlp
  459:             amount,          		 // deposited
  460:             postFeeAmount,      	 // postFeeAmount
  461:             feeAmount          	 // feeAmount
  462:         );
```

Recommended Mitigation Steps
Event-Emit parameter names must match the function arguments or the argument they take value from.
### [L-08] Loss of precision due to rounding


```solidity
src/PirexGmx.sol:
  221      {
  222:         feeAmount = (assets * fees[f]) / FEE_DENOMINATOR;
  223          postFeeAmount = assets - feeAmount;;

```
### [L-09] First value check of argument of type enum in setFee function is missing


```solidity
src/PirexGmx.sol:
  298          @param  fee  uint256  Fee amount
  299:      */
  300:     function setFee(Fees f, uint256 fee) external onlyOwner {
  301:         if (fee > FEE_MAX) revert InvalidFee();
  302: 
  303:         fees[f] = fee;
  304: 
  305:         emit SetFee(f, fee);
  306:     }

```

Leaving the enum type argument `Fees` in the setFee function empty and returning the value `0` gives the same result as returning the value `0`, this is a property of the enum type and therefore error-prone

Recommended Mitigation Step;

Use struck instead of enum

### [L-10] Hardcode the address causes no future updates


```solidity

src/vaults/AutoPxGmx.sol:
  18      IV3SwapRouter public constant SWAP_ROUTER =
  19:         IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45);
```

Router etc. In case the addresses change due to reasons such as updating their versions in the future, addresses coded as constants cannot be updated, so it is recommended to add the update option with the onlyOwner modifier.


### [L-11] Lack of Input Validation

For defence-in-depth purpose, it is recommended to perform additional validation against the amount that the user is attempting to deposit, mint, withdraw and redeem to ensure that the submitted amount is valid.

[OpenZeppelinTokenizedVault.sol#L9](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7c75b8aa89073376fb67d78a40f6d69331092c94/contracts/token/ERC20/extensions/ERC20TokenizedVault.sol#L95)


```diff
src/PirexGmx.sol:
  429       */
  430:     function depositGmx(uint256 amount, address receiver)
  431:         external
  432:         whenNotPaused
  433:         nonReentrant
  434:         returns (
  435:             uint256,
  436:             uint256,
  437:             uint256
  438:         )
  439:     {
+	         require(amount <= maxDeposit(receiver), "deposit more than max");
```

```diff
src/vaults/PirexERC4626.sol:
  79  
  80:     function mint(uint256 shares, address receiver)
  81:         public
  82:         virtual
  83:         returns (uint256 assets)
  84:     {
+ 	       require(shares <= maxMint(receiver), "mint more than max");

```

```diff
src/vaults/AutoPxGlp.sol:
  438:     function withdraw(
  439:         uint256 assets,
  440:         address receiver,
  441:         address owner
  442:     ) public override returns (uint256 shares) {
+                require(assets <= maxWithdraw(owner), "withdraw more than max");

src/vaults/AutoPxGmx.sol:
  317:     function withdraw(
  318:         uint256 assets,
  319:         address receiver,
  320:         address owner
  321:     ) public override returns (uint256 shares) {
+               require(assets <= maxWithdraw(owner), "withdraw more than max");
```


```diff
src/vaults/AutoPxGlp.sol:
  450       */
  451:     function redeem(
  452:         uint256 shares,
  453:         address receiver,
  454:         address owner
  455:     ) public override returns (uint256 assets) {
+               require(shares <= maxRedeem(owner), "redeem more than max");

src/vaults/AutoPxGmx.sol:
  340  
  341:     function redeem(
  342:         uint256 shares,
  343:         address receiver,
  344:         address owner
  345:     ) public override returns (uint256 assets) {
+                require(shares <= maxRedeem(owner), "redeem more than max");
```

### [NC-01] Insufficient coverage

**Description:**
Testing all functions is best practice in terms of security criteria.

```js

| File                              | % Lines           | % Statements      | % Branches       | % Funcs         |
|-----------------------------------|-------------------|-------------------|------------------|-----------------|
| src/PirexRewards.sol              | 98.98% (97/98)    | 99.24% (131/132)  | 94.44% (51/54)   | 94.12% (16/17)  |
| src/PxGmx.sol                     | 100.00% (0/0)     | 100.00% (0/0)     | 100.00% (0/0)    | 0.00% (0/1)     |
| src/vaults/AutoPxGlp.sol          | 91.11% (82/90)    | 93.39% (113/121)  | 87.04% (47/54)   | 94.44% (17/18)  |
| src/vaults/AutoPxGmx.sol          | 89.39% (59/66)    | 91.46% (75/82)    | 71.05% (27/38)   | 92.31% (12/13)  |
| src/vaults/PirexERC4626.sol       | 75.00% (39/52)    | 76.79% (43/56)    | 37.50% (6/16)    | 47.62% (10/21)  |
| src/vaults/PxGmxReward.sol        | 78.12% (25/32)    | 79.49% (31/39)    | 50.00% (5/10)    | 75.00% (3/4)    |
| Total                             | 56.72% (523/922)  | 58.93% (660/1120) | 59.95% (241/402) | 47.87% (90/188) |

```

Due to its capacity, test coverage is expected to be 100%

### [NC-02] Not using the named return variables anywhere in the function is confusing


```solidity

 function getUserState(ERC20 producerToken, address user)
        external
        view
        returns (
            uint256 lastUpdate,
            uint256 lastBalance,
            uint256 rewards
        )
    {
        UserState memory userState = producerTokens[producerToken].userStates[
            user
        ];

        return (userState.lastUpdate, userState.lastBalance, userState.rewards);
    }

```

**Recommendation:**
Consider adopting a consistent approach to return values throughout the codebase by removing all named return variables, explicitly declaring them as local variables, and adding the necessary return statements where appropriate. This would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.

### [NC-03] Same Constant redefined elsewhere

Keeping the same constants in different files may cause some problems or errors, reading constants from a single file is preferable. This should also be preferred in gas optimization


```solidity

src/vaults/AutoPxGlp.sol:
  17  
  18:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
  19:     uint256 public constant MAX_PLATFORM_FEE = 2000;
  20:     uint256 public constant FEE_DENOMINATOR = 10000;
  21:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

src/vaults/AutoPxGmx.sol:

  20:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
  21:     uint256 public constant MAX_PLATFORM_FEE = 2000;
  22:     uint256 public constant FEE_DENOMINATOR = 10000;
  23:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
```

### [NC-04] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;

```solidity
8 results 

src/PirexFees.sol:
  63:     function setFeeRecipient(FeeRecipient f, address recipient)

  83:     function setTreasuryFeePercent(uint8 _treasuryFeePercent)

src/PirexGmx.sol:
  300:     function setFee(Fees f, uint256 fee) external onlyOwner {
  301          if (fee > FEE_MAX) revert InvalidFee();

  313:     function setContract(Contracts c, address contractAddress)

  862:     function setDelegationSpace(
  863          string memory _delegationSpace,

  884:     function setVoteDelegate(address voteDelegate) external onlyOwner {
  885          if (voteDelegate == address(0)) revert ZeroAddress();

  909:     function setPauseState(bool state) external onlyOwner {
  910          if (state) {

src/PirexRewards.sol:
   93:     function setProducer(address _producer) external onlyOwner {
   94          if (_producer == address(0)) revert ZeroAddress();

```

### [NC-05] Add parameter to Event-Emit

Some event-emit description hasn’t parameter. Add to parameter  for front-end website or client app , they can has that something has happened on the blockchain.

Events with no old value;

```solidity

src/PirexGmx.sol:
  894       */
  895:     function clearVoteDelegate() public onlyOwner {
  896:         emit ClearVoteDelegate();

```

### [NC-06] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

**Context:**

```solidity
src/vaults/PxGmxReward.sol:
  15:     ERC20 public pxGmx;
  17:     GlobalState public globalState;
  18:     uint256 public rewardState;
  19:     mapping(address => UserState) public userRewardStates;
```

### [NC-07] Use `require` instead of `assert`


```solidity

src/PirexGmx.sol:
  224  
  225:         assert(feeAmount + postFeeAmount == assets);
  226      }

```
**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".



### [NC-08] Implement some type of version counter that will be incremented automatically for contract upgrades

[PirexRewards.sol#L85](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L85)

I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js

uint256 public authorizeUpgradeCounter;

 function upgradeTo(address _newImplementation) external onlyOwner {
        _setPendingImplementation(_newImplementation);
       authorizeUpgradeCounter+=1;

    }
```
### [NC-09] Constant values such as a call to keccak256(), should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.


```js
2 results 

src/PxERC20.sol:
    9:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  10:     bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");

```
### [N-10 ] For functions, follow Solidity standard naming conventions

**Context:**
[AutoPxGlp.sol#L487](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L487)
[AutoPxGlp.sol#L501](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L501)
[AutoPxGlp.sol#L474](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L474)
[AutoPxGlp.sol#L462](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L462)

**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [NC-11] Mark visibility of initialize(...) functions as ``external``

[L1GraphTokenGateway.sol#L99](https://github.com/code-423n4/2022-10-thegraph/blob/main/contracts/gateway/L1GraphTokenGateway.sol#L99)

**Description:**
If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}_init function, not the parent public initialize(...) function.

External instead of public would give more the sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change initialize(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750
### [NC-12] No same value input control


```solidity

src/PirexFees.sol:
  63:     function setFeeRecipient(FeeRecipient f, address recipient)

  83:     function setTreasuryFeePercent(uint8 _treasuryFeePercent)

src/PirexGmx.sol:

  313:     function setContract(Contracts c, address contractAddress)

  884:     function setVoteDelegate(address voteDelegate) external onlyOwner {

  909:     function setPauseState(bool state) external onlyOwner {

src/PirexRewards.sol:
   93:     function setProducer(address _producer) external onlyOwner {

  107:     function setRewardRecipient(

  432:     function setRewardRecipientPrivileged(
```

**Recommendation:**
Add code like this;
`if (oracle == _oracle revert ADDRESS_SAME();`

### [NC-13] Include ``return parameters`` in _NatSpec comments_

**Context:**
All contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with "/// @return".

Some code analysis programs do analysis by reading NatSpec details, if they can't see the "@return" tag, they do incomplete analysis.

**Recommendation:**
Include return parameters in NatSpec comments

Recommendation Code Style:
 ```js
    /// @notice information about what a function does
    /// @param pageId The id of the page to get the URI for.
    /// @return Returns a page's URI if it has been minted 
    function tokenURI(uint256 pageId) public view virtual override returns (string memory) {
        if (pageId == 0 || pageId > currentId) revert("NOT_MINTED");

        return string.concat(BASE_URI, pageId.toString());
    }
```
### [NC-14] `0 address` check for ` asset `

**Context:**

```solidity
src/vaults/PirexERC4626.sol:
  47  
  48:     constructor(
  49:         ERC20 _asset,
  50:         string memory _name,
  51:         string memory _symbol
  52:     ) ERC20(_name, _symbol, _asset.decimals()) {
  53:         asset = _asset;
  54:     }

```

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (_asset == address(0)) revert ADDRESS_ZERO();`

### [NC-15] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.left
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
21 results - 6 files

src/PirexFees.sol:
  20:     uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
  23:     uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

src/PirexGmx.sol:
  44:     uint256 public constant FEE_DENOMINATOR = 1_000_000;
  47:     uint256 public constant FEE_MAX = 200_000;

src/PxERC20.sol:
   9:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  10:     bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");

src/external/RewardTracker.sol:
  772:     uint256 public constant BASIS_POINTS_DIVISOR = 10000;
  773:     uint256 public constant PRECISION = 1e30;
  775:     uint8 public constant decimals = 18;

src/vaults/AutoPxGlp.sol:
  18:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
  19:     uint256 public constant MAX_PLATFORM_FEE = 2000;
  20:     uint256 public constant FEE_DENOMINATOR = 10000;
  21:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
  22:     uint256 public constant EXPANDED_DECIMALS = 1e30;
  23  

src/vaults/AutoPxGmx.sol:
  18:     IV3SwapRouter public constant SWAP_ROUTER =
  20:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
  21:     uint256 public constant MAX_PLATFORM_FEE = 2000;
  22:     uint256 public constant FEE_DENOMINATOR = 10000;
  23:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

```

### [NC-16] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last
### [NC-17] Missing Upgrade Path for `PirexRewards` Implementation

```solidity

src/PirexRewards.sol:
   4: import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
  85:     function initialize() public initializer {

```

With the current and contracts, it is not possible to upgrade the contract. 

Recommendation: Document the operational plan for contract upgrades. Also, consider using the UUPS proxy pattern to upgrade contract implementation.

### [NC-18] No need `assert` check in `_computeAssetAmounts()`

The `assert` check in this function is not needed. Because this check will always be true because of this line:
 `postFeeAmount = assets - feeAmount;`

**Context:**
```diff
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
- 227:       assert(feeAmount + postFeeAmount == assets);
  228:     }

```


### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
### [S-02] Add NatSpec comments to the variables defined in Storage

**Description:**
I recommend adding NatSpec comments explaining the variables defined in Storage, their slots, their contents and definitions.

This improves code readability and control quality


Current Code;
```solidity

src/vaults/AutoPxGlp.sol:
  18:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
  19:     uint256 public constant MAX_PLATFORM_FEE = 2000;
  20:     uint256 public constant FEE_DENOMINATOR = 10000;
  21:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
  22:     uint256 public constant EXPANDED_DECIMALS = 1e30;
  24:     uint256 public withdrawalPenalty = 300;
  25:     uint256 public platformFee = 1000;
  26:     uint256 public compoundIncentive = 1000;
  27:     address public platform;
  30:     address public immutable rewardsModule;
```

Recommendation Code;

```solidity

 //****** Slot 0 ******//
 26:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;

 / /****** Slot 1 ******//
 30:     uint256 public constant MAX_PLATFORM_FEE = 2000;

  //****** Slot 2 ******//
 33:     uint256 public constant FEE_DENOMINATOR = 10000;
...

  /****** End of storage ******/
```
