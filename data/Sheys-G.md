# Use custom errors instead of required statements in these lines of code
## 1. AutoPxGmx [L355](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L355)
```
            require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

Use this instead:
```
    if((assets = previewRedeem(shares)) == 0) revert ZeroAssets();
```

## 2. PirexERC4626 [L68](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L68)
```
            require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
```

Use this instead:
```
    if((shares = previewDeposit(assets)) == 0) revert ZeroShares();
```

## 3. PirexERC4626 [L137](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L137)
```
            require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

Use this instead:
```
    if((assets = previewRedeem(shares)) == 0) revert ZeroAssets();
```