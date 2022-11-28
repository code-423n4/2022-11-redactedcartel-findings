### Upgrade pragma to 0.8.17 to save gas

Upgrade PirexERC4626.sol to solidity 0.8.17 (like the rest of the solution) to ensure gas optimization.

```
src/vaults/PirexERC4626.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity >=0.8.0;
  3
```  

Upgrading to 0.8.17 has many benefits, cheaper on gas is one of them.
The following information is regarding 0.8.15 over 0.8.14. Assume that 0.8.17 over 0.8.0 the save is higher!
```
According to the release note of 0.8.15: https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/
The benchmark shows saving of 2.5-10% Bytecode size,
Saving 2-8% Deployment gas,
And saving up to 6.2% Runtime gas.
```

