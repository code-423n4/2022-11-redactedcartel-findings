# Low Risk Findings
## 1 - Contracts can't withdraw accidentally sent tokens and are distributed though rewards
**Description**
Using balanceOf for reward distribution means accidentally sent reward tokens to the contract will be distributed to all users who are eligible for rewards. In the claimRewards() function of PirexGmx.sol baseRewardBeforeClaim variable is calculated like this: `uint256 baseRewardBeforeClaim = gmxBaseReward.balanceOf(address(this));`. 

There are other instances of this vulnerability where the user can't get back their tokens if they accidentally send it to contracts listed in codebase references.
 
 **Exploit Scenario**
A user accidentally sent WETH (base reward) to the PirexGmx.sol contract without calling any functions. The contract doesn't have any functions to rescue these funds and will be distributed as rewards when PirexRewards calls the claimRewards() function.

**Codebase references**
 - [PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100) - distributeFees()
 - [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L210) - compound()
 - [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L242) - compound()

**Recommendation**
Examine and consider the trade-offs of using internal accounting instead of `balanceOf(address(this)` . Implement functions that allow to withdraw the accidentally sent tokens from the contracts.

## 2 - PirexERC4626's ERC20 asset can be incorrectly initialized and Solmate's safeTransfer would indicate it's working correctly
**Description**
Solmate's safeTransfer doesn't check if the token has code and will return a successful call even if the address doesn't have any code deployed to.

**Exploit Scenario**
If [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol) or [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol) contracts have an incorrectly initialized asset to a non-existent address (see [PirexERC4626.sol - L53](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L53)) returning calls from deposit(), mint() withdraw() and redeem() will not revert and return successful calls indicating transfers work correctly. In this case there is nothing else to do (since asset is immutable) except to redeploy said contracts.

**Codebase References**

 - PirexERC4626 - [constructor asset intialization](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L53)
 - PirexERC4626 - [deposit()](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L60), [mint()](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L80), [withdraw()](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L99), [redeem()](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L124)

**Recommendation**
In the constructor of PirexERC4626.sol check if the ERC20 asset address has code before initializing the asset in the constructor. Long term be mindful when developing that Solmate's transfer library doesn't check if an address it's used on has code deployed to it.


## 3 - Receiver input on deposit and redeem functions can be accidentally set to address(this) or any other Pirex contract
**Description**
The receiver input on all the deposit and redeem functions of PirexGmx.sol can be set to the same contract or other Pirex contracts that can't withdraw. This would mean the user lost the deposited assets. Similar issue to Low Risk #1

**Exploit Scenario**
A user misunderstanding how depositGmx() works (in PirexGmx.sol) wanting to deposit GMX accidentally set the receiver input to the PirexGmx.sol contract address. Now there is no way to recover their funds.

**Codebase References**

 - PirexGmx.sol - [L378-L731 all functions](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L378-#L731)

**Recommendation**
Add a check in the internal _deposit and _redeem functions `if (receiver == address(this)) revert WrongAddress();`

## 4 - Passing incorrect pirexRewards constructor parameter in PxERC20 would lead reward functionality not working
**Description**
If pirexRewards were set incorrectly to a wrong address when deploying PxErc20, the contract wouldn't have any reward functionality but would still appear as working. In this case the contract has to be redeployed. 

Another scenario where this is problematic is when pirexRewards would have a new contract deployed: because pirexRewards is immutable and can't be set to another address PxERC20 has to be redeployed in this case as well.

**Codebase References**
 - PxERC20.sol - [L25](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L25)

**Recommendation**
Explore and consider the trade-offs of using a pirexRewards setter function.

## 5 - Contract (PirexERC4626) used as dependency does not track upstream changes
**Description**
PirexERC4626 uses a modified version of solmate's ERC4626 implementation, but the documentation does not specify which version or commit is used. This indicates that the protocol does not track upstream changes in contracts used as dependencies. Therefore, contracts may not reflect updates or security fixes implemented in their dependencies, as these updates need to be manually integrated.

**Exploit Scenario**
Third-party contract (Solmate's ERC4626) used in Pirex receives an update with a critical fix for a vulnerability, but the update is not yet manually integrated in the current version of the protocol. An attacker detects the use of vulnerable ERC4626 contract in the protocol and exploits the vulnerability.

**Codebase References**
 - [PirexERC4626.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol)

**Recommendation**
Review the codebase and document the source and version of each dependency. Include third-party sources as modules in the project to maintain path consistency and ensure dependencies are updated periodically.


# Non-Critical Findings
## 1 - Fee denominator is inconsistent across contracts
**Description**
The constant FEE_DENOMINATOR declared in [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44) is 1_000_000, but in [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L20) and [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L22) it is 10000. In [PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20) it's called FEE_PERCENT_DENOMINATOR and is 100.

**Codebase References**

 - [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44) - L44
 - [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L20) - L20
 - [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L22) - L22
 - [PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20) - L20

**Recommendation**
Consider using the same FEE_DENOMINATOR name and value to be more consistent across the codebase.

 ## 2 - Enums used in functions and events are undescriptive
 **Description**
 All the enums used in functions and events are represented by a single letter. For example in [PirexGmx.sol on L239](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L239) RewardTracker enum is used as r. This is bad for clarity and readability.
 
**Codebase references**:

 - [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol) - L95-L96, L239, L300-305, L313-365, L503-507
 - [PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol) - L34, L63-76
 - [PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol) - L159-160, L285, L289-295, L352-363, L380-391
 
**Recomendation**
Instead of using single letters, consider writing more descriptive enums so it's easier to read and make sense of. For example instead of r, the enum inside the function could be named rTracker or rewardTracker or tracker.
 
 ## 3 - Certain event parameters should be indexed
 **Description**
 Events emitting notable changes (for example [initiating and completing migration](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L140-#L141)) are not indexed. The negligible gas costs that indexing adds may be a worthwhile trade-off to make tracking these event parameters easier.
 
 **Codebase References**
 - [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L140-L143) - L140, L141, L,142 (delegationSpace), L143
 - [PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L35) - L35 
 - [PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L34) - L34
 - [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L38) - L38
 - [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L43) - L43
 
**Recommendation**
Consider adding indexed to parameters of events emitting notable changes. See these events above in the references.

## 4 - Inconsistent return values of functions
**Description**
Return values of functions are inconsistent (named and unnamed through the codebase).

**Codebase References**
 - [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol): unnamed - depositGmx, depositFsGlp, depositGlpETH, depositGlp, redeemPxGlpETH, redeemPxGlp
 - [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol): named - _depositGlp, _redeemPxGlp
 
 **Recommendation**
Consider naming unnamed return values, especially those that have commented return value descriptions for more clarity

## 5 - Assert should only be used to assert invariants
**Description**
In [PirexGmx.sol L225](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225) assert is used. Assert is meant for asserting invariants. See the references link below

**Recommendation**
Use revert or require instead of assert().

**References** 
[SWC-110](https://swcregistry.io/docs/SWC-110)
 

## 6 - Same errors and events repeated across contracts
**Description**
A lot of errors in different contracts are the same. For example ZeroAmount() error is in 4 different contracts ([PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol), [PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol), [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol), [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)).  
[AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol) and [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol) contracts have the most similar errors and events. 

**Codebase References**

 - [PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)
 - [PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)
 - [AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)
 - [AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

**Recommendation**
Consider writing a library and inheriting it in contracts for repeated errors and events so code stays DRY and not repeated.

## 7 - Same functions repeated in AutoPxGlp.sol and AutoPxGmx.sol
**Description**
setWithrawalPenalty(), setPlatformFee(), setCompoundIncentive() and setPlatform() are used in both AutoPxGlp.sol and AutoPxGmx.sol with the same functionality.

**Codebase references** 

 - AutoPxGlp.sol - [same functions](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94-#L136)
 - AutoPxGmx.sol - [same functions](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116-#L158)

**Recommendation**
Consider writing an abstract contract that have these functions implemented and inherit it in both contracts. 

## 8 - PirexRewards.sol setRewardRecipient() allows setting same producer and reward token address
**Description**
Producer tokens  (pxGMX, pxGLP) are different from reward tokens (WETH, WAVAX, etc.), yet setRewardRecipient() allows setting the same address for both producerToken and rewardToken.

**Codebase references**

 - PirexGmx.sol - [setRewardRecipient()](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L107-#L126)

**Recommendation**
Add a check `if (address(producerToken) == address(rewardToken)) revert WrongAddress();` to prevent incorrect setting. 

## 9 - Rename producer to pirexGmx for consistency through the codebase to be consistent
**Description**
It is not intuitive that producer refers to the pirexGMX contract.

**Recommendation**
Consider renaming producer to pirexGmx through all the contracts.

## 10 - Rename rewardsModule to pirexRewards in AutoPxGmx.sol and AutoPxGlp.sol for consistency
**Description**
It is not intuitive that rewardsModule refers to the pirexRewards contract.

**Recommendation**
Consider renaming producer to pirexRewards through all the contracts.

## 11 - Use more descriptive name for newAssets in AutoPxGlp.sol compound()
**Description**
newAsset doesn't describe the variable in AutoPxGlp.sol compound() fully.

**Codebase References**

 - AutoPxGlp.sol - [compound() - newAssets](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L250)
  
 **Recommendation**
 Consider renaming newAssets to a more descriptive name like assetsOut or assetsAmountOut or assetsToBeDistributed.

## 12 - No Uniswap V3 poolFee in AutoPxGlp.sol
**Description**
In the contest repository README AutoPxGlp.sol has a description of 

>  Provides a series of permissioned methods that enables the Pirex multisig to configure fees, incentives, and Uniswap V3 pool fee

but there is no method or interaction with a UniswapV3 pools in AutoPxGlp.sol

**Recommendation**
Remove this description or add Uniswap V3 functionality and fee configuration.

## 13 - In AutoPxGlp.sol constructor _platform doesn't need address() casting since it's already an address type
**Description**
In the AutoPxGlp.sol constructor on L87 inside a safeApprove the _platform variable is casted to address: `gmxBaseReward.safeApprove(address(_platform), type(uint256).max);`. Since _platform is already an address passed in the constructor it doesn't need any casting.

**Codebase References**

 - AutoPxGlp.sol - constructor - [L87](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L87)

**Recommendation**
Remove address() casting, use `gmxBaseReward.safeApprove(_platform), type(uint256).max)`; instead.

## 14 - Use more extensive natspec documentation
**Description**
Variables, structs, modifiers and events sometimes lack documentation through the codebase. 

**Recommendation**
Consider documenting every variable, struct, modifier, event or at least documenting the critical ones so the reader of the contracts have an easier time understanding everything.