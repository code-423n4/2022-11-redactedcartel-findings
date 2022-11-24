G1. `previewWithdraw` and `previewRedeem` in `AutoPxGlp.sol` can be made external to save gas, since they are not called internally.

G2. `harvest()` can be made more gas efficient if it keeps track of lastCallTime and returns immediately if lastCallTime is the same as current block.timeStamp
