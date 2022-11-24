


1. Unchecking arithmetics operations that can’t underflow/overflow:-
While this is inside an external view function, consider wrapping this in an unchecked statement so that external contracts calling this might save some gas

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L798
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L795
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L433
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L802
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L805
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L785
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L787
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L289
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L403
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L53
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L102


2. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops:-

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396


3. storage pointer to a structure is cheaper than copying each value of the structure into memory, same for array and mapping:-

It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L160
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L216
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L386


4. Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas.

code snippet :-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L10

5 . Mark functions as payable when users can't mistakenly send ETH :-
Functions marked as payable are 24 gas cheaper than their counterpart (in non-payable functions, Solidity adds an extra check to ensure msg.value is zero).
When users can't mistakenly send ETH to a function (as an example, when there's an onlyOwner modifier or alike), it is safe to mark it as payable.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300
