
---

## Summary
 
- G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 4 instances
- G-02 internal functions only called once can be inlined to save gas | 4 instances
- G-03 Empty blocks should be removed or emit something | 7 instances
- G-04 Public functions to external | 5 instances

Total: 20 instances in 4 issues

---


## G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

Instances (4):

PirexRewards.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L361

PirexRewards.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L124

vaults/PxGmxReward.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95



## G-02 internal functions only called once can be inlined to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Instances (4):

vaults/AutoPxGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L222

vaults/AutoPxGlp.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L474
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L487
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L501
 

## G-03 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. 
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

Instances (7):

PxGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L12
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L23

vaults/PirexERC4626.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L276
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L282
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L288
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L294
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L300



## G-04 Public functions to external
Public functions not called by the contract should be declared external instead. 
Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 
https://docs.soliditylang.org/en/latest/contracts.html#function-overriding

Instances (5):

PxERC20.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L113

PirexRewards.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L85

vaults/AutoPxGlp.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L151
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L177
 
