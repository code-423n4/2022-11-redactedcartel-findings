
# Gas

| | issue |
| ----------- | ----------- |
| 1 | [State variables only set in the constructor should be declared immutable.](#1-state-variables-only-set-in-the-constructor-should-be-declared-immutable) |
| 2 | [state variables can be packed into fewer storage slots](#2-state-variables-can-be-packed-into-fewer-storage-slots) |
| 3 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#3-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 4 | [Stack variable used as a cheaper cache for a state variable is only used once](#4-stack-variable-used-as-a-cheaper-cache-for-a-state-variable-is-only-used-once) |
| 5 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#5-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 6 | [not using the named return variables when a function returns, wastes deployment gas](#6-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 7 | [can make the variable outside the loop to save gas](#7-can-make-the-variable-outside-the-loop-to-save-gas) |
| 8 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#8-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 9 | [internal functions only called once can be inlined to save gas](#9-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 10 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#10-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 11 | [public functions not called by the contract should be declared external instead](#11-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 12 | [state var is defined but not used anywhere but](#12-state-var-is-defined-but-not-used-anywhere-but) |
| 13 | [should pre calculate constant instead of using external call](#13-should-pre-calculate-constant-instead-of-using-external-call) |

## 1. State variables only set in the constructor should be declared immutable.

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmaccess (100 gas) with a PUSH32 (3 gas).

`pxGmx`
- [PxGmxReward.sol#L15](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L15)


## 2. state variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

make `poolFee` before `platform` so they be in one slot
- [AutoPxGmx.sol#L26](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L26)


## 3. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [PxERC20.sol#L9](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9)
- [PxERC20.sol#L10](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L10)


## 4. Stack variable used as a cheaper cache is only used once

If it is only accessed once, it’s cheaper to use the function directly that one time, and save the 3 gas the extra stack assignment would spend

- [PirexERC4626.sol#L243](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L243)
- [PirexERC4626.sol#L261](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L261)


## 5. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [PirexRewards.sol#L361](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L361)

- [PxERC20.sol#L85](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85)
- [PxERC20.sol#L90](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90)
- [PxERC20.sol#L119](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L119)
- [PxERC20.sol#L124](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L124)

- [PxGmxReward.sol#L95](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95)


## 6. not using the named return variables when a function returns, wastes deployment gas

- [PirexRewards.sol#L11-L13](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L11-L13)


## 7. can make the variable outside the loop to save gas

make the variables before the loop and only assign them inside.

- [PirexRewards.sol#L352](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L352)
- [PirexRewards.sol#L353](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L353)
- [PirexRewards.sol#L397-L403](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L397-L403)


## 8. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [PirexRewards.sol#L163](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163)
- [PirexRewards.sol#L351](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351)
- [PirexRewards.sol#L396](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396)


## 9. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`afterDeposit`
- [AutoPxGlp.sol#L474](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L474)

`beforeDeposit`
- [AutoPxGmx.sol#L379](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L379)

`_harvest`
- [PxGmxReward.sol#L90](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L90)


## 10. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [PirexFees.sol#L20](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20)
- [PirexFees.sol#L23](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23)
- [PirexFees.sol#L28](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28)
- [PirexFees.sol#L83](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83)


## 11. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

- [PxERC20.sol#L80](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80)
- [PxERC20.sol#L109](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L109)

- [AutoPxGlp.sol#L436](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L436)
- [AutoPxGlp.sol#L177](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L177)
- [AutoPxGlp.sol#L151](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L151)

- [AutoPxGmx.sol#L339](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L339)
- [AutoPxGmx.sol#L315](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L315)

- [PirexERC4626.sol#L229](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L229)
- [PirexERC4626.sol#L225](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L225)
- [PirexERC4626.sol#L221](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L221)
- [PirexERC4626.sol#L217](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L217)
- [PirexERC4626.sol#L124](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L124)
- [PirexERC4626.sol#L99](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L99)
- [PirexERC4626.sol#L80](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L80)
- [PirexERC4626.sol#L60](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L60)


## 12. state var is defined but not used anywhere but 

even if they are for readability, consider making them comments instead

`EXPANDED_DECIMALS`
- [AutoPxGlp.sol#L22](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L22)


## 13. should pre calculate constant instead of using external call

`SWAP_ROUTER` will always be the same so instead of using `IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45)` to calculate it just calculate it once before and only give the answer to the constant so its not calculated every time contract is used.

- [AutoPxGmx.sol#L18](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L18)
