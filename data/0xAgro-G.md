# Gas Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[G-01]|`uint<size>` Used Rather Than `uint256`|4|
|[G-02]|`uint256` Iterator Checked Each Iteration|3|
|[G-03]|&& In If Statement(s)|1|

### [G-01] `uint<size>` Used Rather Than `uint256`

The EVM deals with 32 bytes at a time. For `uint<size>`s less than 32 bytes the EVM performs more operations as a result of the needed size reduction from `uint256` to `uint<size>`.

#### Findings:

*/src/PirexFees.sol*
Links: [20](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L20), [23](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L23), [28](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L28).
```solidity
20:	uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
23:	uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
28:	uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;
```

*/src/vaults/AutoPxGmx.sol*
Links: [26](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L26).
```solidity
26:	uint24 public poolFee = 3000;
```

### [G-02] `uint256` Iterator Checked Each Iteration

A `uint256` iterator will not overflow. Unchecking the iterator increment saves gas.
**Example**
```solidity
//From
for (uint256 i; i < len; ++i) {
	//Do Something
}
//To
for (uint256 i; i < len;) {
	//Do Something
	unchecked { ++i; }
}
```

#### Findings:

*/src/PirexRewards.sol*
Links: [163](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163), [351](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351), [396](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396).
```solidity
163:	for (uint256 i; i < len; ++i) {
351:	for (uint256 i; i < pLen; ++i) {
396:	for (uint256 i; i < rLen; ++i) {
```

### [G-03] && In If Statement(s)

Seperating if statements saves gas.
**Example**
```solidity
//From
if (a != HIGH && b != LOW) {
	//Do Something
}
//To
if (a != HIGH) {
	if (b != LOW) {
		//Do Something
	}
}
```

#### Findings:

*/src/PirexRewards.sol*
Links: [385](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L385).
```solidity
385:	if (globalRewards != 0 && userRewards != 0) {
```