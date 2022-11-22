1 .Use private instead of Public for Constant variable to save gas :-
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

Deployment Gas Saved: 45 857
Method Call Gas Saved: 308

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L18
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L19
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L20
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L22
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L47
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L18
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L20
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L22
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L10
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28

2. Use assembly to check for address(0) (65 instances) :-

Use assembly to check for address(0) Saves 6 gas per instance if using assembly to check for address(0) e.g.

assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L41
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L376
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L153
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L82
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L83
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L84
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L87
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L88
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L421
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L374
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L378
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L336
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L75
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L76
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L77
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L78
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L79
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L80
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L467
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L468
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L439
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L440
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L441
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L374
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L375
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L271
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L283
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L282
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L182
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L183
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L184
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L185
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L186
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L187
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L188
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L189
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L190
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L191
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L317
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L388
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L389
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L432
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L433
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L497
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L630
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L727
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L829
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L831
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L885
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L926
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L961
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L94
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L112
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L113
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L114
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L136
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L137
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L155
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L156
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L69
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L106
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L30
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L67
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L51
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L52


3. Unchecking arithmetics operations that can’t underflow/overflow:-
While this is inside an external view function, consider wrapping this in an unchecked statement so that external contracts calling this might save some gas

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L795
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L433
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L802
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L785
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L787
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L289
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L403
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L53
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L102


4. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops:-

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396


5. storage pointer to a structure is cheaper than copying each value of the structure into memory, same for array and mapping:-

It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L160
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L216
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L386

6. Use custom errors instead of string in require statements :-

Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L355

7. Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas.

code snippet :-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L10

