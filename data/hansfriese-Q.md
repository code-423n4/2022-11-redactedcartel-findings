### [L-01] Wrong comment
- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L86

```solidity
    // Approve the Uniswap V3 router to manage our base reward (inbound swap token)
```

It should be removed as this contract doesn't use the swap router.

### [L-02] Wrong parameter for `beforeDeposit()`
- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L65

```solidity
    if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);

    // Check for rounding error since we round down in previewDeposit.
    require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
```

It calls `beforeDeposit()` before `shares` is calculated so `shares` will be 0 always.

Although `beforeDeposit()` doesn't use the parameters now, it's recommened to pass correct values. (Or we can just use 0 for all).

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L85

```solidity
    if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);

    assets = previewMint(shares); // No need to check for rounding error, previewMint rounds up.
```

It's almost the same as the above case and `assets` is used before calculated. 

### [N-01] Typo
- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L336

```solidity
    @return rewardAmounts    ERC20[]  Reward token amounts
```

Change `ERC20[]` to `uint256[]`.