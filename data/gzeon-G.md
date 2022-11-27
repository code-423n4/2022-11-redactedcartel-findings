Since GMX is on Arbitrum, a L2 rollup, it is much more important to optimize for calldata when considering gas optimization. In fact, EVM gas optimizations have minimal effect when compared to calldata optimizations. For example at 9 gwei L1 gas price and 0.1 gwei Arbitrum gas price, each byte of calldata after compression cost ~640 calldata gas. Here are some suggestions to reduce the calldata size:

1. For all the function with a `receiver` parameter (e.g. `depositGmx(uint256 amount, address receiver)`), add a helper function which default it with msg.sender. This saves 20 bytes (or 32 bytes, but those 0 will mostly be compressed away) of calldata ~= 12800 gas.

2. For deposit and withdraw functions, allow the user to specify some magic value to deposit/withdraw max. This saves 32 bytes of calldata ~= 20480 gas.

3. On the UI level, use more compressible value (e.g. 1024(0x400) instead of 1000(0x3E8)) for amounts like minGlp

4. Consider integrate with [ArbAddressTable](https://developer.offchainlabs.com/arbitrum-ethereum-differences#precompiles) for token addresses and other common addresses.

Resources
- https://developer.offchainlabs.com/
- https://l2fees.info/blog/rollup-calldata-compression