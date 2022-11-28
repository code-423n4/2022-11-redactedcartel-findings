1 - Use ```totalAssets()``` function.
==

Use the ```totalAssets()``` function in the corresponding contract.

**Instances of this issue:**
- [src/vaults/AutoPxGmx.sol#L260](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L260)
- [src/vaults/AutoPxGlp.sol#L229](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L229)

2 - Save the newAssets calculation in a variable and use it in the condition and next calculation
==

Save the newAssets calculation in a variable here [src/vaults/AutoPxGmx.sol#L288](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L288) and use it in the [290 line](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L290). Basically it follows the same pattern from [src/vaults/AutoPxGlp.sol#L250](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L250)

Example:

```solidity
uint256 newAssets = totalAssets() - assetsBeforeClaim;
if (newAssets != 0) {
	totalFee = (newAssets * platformFee) / FEE_DENOMINATOR;
}
```
