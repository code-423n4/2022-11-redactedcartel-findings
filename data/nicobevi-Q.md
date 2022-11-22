# QA

## Extends contracts using the correspinding interfaces
A good practice when you have an interface representing a contract is to extend such contract with the corresponding interface. In that way you can be sure that the interface definition is accurate.

### Where
1. `./vaults/AutoPxGlp.sol` should use `./interfaces/IAutoPxGlp.sol`
2. `./PirexRewards.sol` should use `./interfaces/IPirexRewards.sol`