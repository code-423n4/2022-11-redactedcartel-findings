G1. `previewWithdraw` and `previewRedeem` in `AutoPxGlp.sol` can be made external to save gas, since they are not called internally.

G2. `harvest()` can be made more gas efficient if it keeps track of lastCallTime and returns immediately if lastCallTime is the same as current block.timeStamp

G3. 
totalAssets() and asset.balanceOf(address(this)) does the same thing, so create one local variable to save one SLOAD
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L288