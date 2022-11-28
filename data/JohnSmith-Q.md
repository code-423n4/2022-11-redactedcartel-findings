## [L-01] Disable initializers
As per OpenZeppelin recommendation [Initializing the Implementation Contract](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#initializing_the_implementation_contract)
> Do not leave an implementation contract uninitialized. An uninitialized implementation contract can be taken over by an attacker, which may impact the proxy. 
### Findings
```
src/PirexRewards.sol
15: contract PirexRewards is OwnableUpgradeable {
```
### Mitigation
To prevent the implementation contract from being used, you should invoke the `_disableInitializers` function in the constructor to automatically lock it when it is deployed:
```
/// @custom:oz-upgrades-unsafe-allow constructor
constructor() {
    _disableInitializers();
}
```

## [L-02] `safeApprove()` is deprecated
`safeApprove` is deprecated in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. 
Some tokens do not allow approving an amount `M > 0` when an existing amount `N > 0` is already approved.
If GMX adds to whitelisted such token functions `depositGlp` will revert
### Findings
```solidity
src/PirexGmx.sol
507:             t.safeApprove(glpManager, tokenAmount);//@audit check for approve(0) for whitelisted/not whitelisted coins
```

```solidity
src/vaults/AutoPxGlp.sol
391:             erc20Token.safeApprove(platform, tokenAmount);
```