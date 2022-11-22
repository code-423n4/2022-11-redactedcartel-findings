## Non-critical

### Duplicated import

[Location](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L8)

#### Description

`AutoPxGlp` duplicates an import `SafeTransferLib`.

#### Suggested course of action

Remove duplicated import statement.

## Non-critical

### Functions use two different syntaxes to accomplish the same thing

Locations:
[1](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L229)
[2](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L260)
[3](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L290)

#### Description

Two contracts that derive from `PirexERC4626` contain mixed uses of `asset.balanceOf(address(this))` and a function call `totalAssets()` that does the same thing. This makes code comprehension more difficult, as the assumption would be that the functionalized version of the action does something different than a simple `balanceOf` check.

#### Suggested course of action

Use `totalAssets()` or `asset.balanceOf(address(this))` consistently. 

Also, consider setting the implementation in `PirexERC4626` since there is no difference in the implementation of the overridden version of the function.

## Non-critical

### Unused error defined

[Location](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L60)


#### Description

Contract defines an error that isn't used. This can indicate a drift from an intended specification, as well as gap in tests.

#### Suggested course of action

Include the error in a function, or remove the error from the contract.