## [NAZ-G] Unused State Variables
**Context**: [`AutoPxGlp.sol#L22`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L22)

**Description**:
There are some unused state variables that are taking up storage with costs extra unwanted gas.

**Recommendation**:
Consider removing the unused state variables or use them.


## [NAZ-G] Use Assembly To Set Storage Variables 
**Context**: [`PirexFees.sol#L63`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L63), [`PirexFees.sol#L83`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83), [`PirexGmx.sol#L313`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L313), [`PirexGmx.sol#L862`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L862), [`PirexGmx.sol#L921`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L921), [`PirexRewards.sol#L93`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93), [`AutoPxGlp.sol#L94`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94), [`AutoPxGlp.sol#L106`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106), [`AutoPxGlp.sol#L118`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118), [`AutoPxGlp.sol#L130`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130), [`AutoPxGmx.sol#L104`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104), [`AutoPxGmx.sol#L116`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116), [`AutoPxGmx.sol#L128`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128), [`AutoPxGmx.sol#L140`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140), [`AutoPxGmx.sol#L152`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152)

**Description**:
Using assembly to set storage varaible is cheaper in gas than setting it normally. This also doesn't degrade readability for the code is still self-explanatory EX:
```js
contract SetExpensive {
    uint256 public set;

    // ~22_520 gas
    function setIt(uint256 _set) public {
        set = _set;
    }
}

contract SetOptimized {
    uint256 public set;
    // ~22_512 gas
    function setIt(uint256 _set) public {
        assembly {
            sstore(set.slot, _set)
        }
    }
}
// ~8 gas cheaper
```

**Recommendation**: 
Consider using assembly to set storage varaibles.


## [NAZ-G] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`PirexRewards.sol#L163`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163), [`PirexRewards.sol#L351`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351), [`PirexRewards.sol#L396`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.
