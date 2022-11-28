# QA Report (low / non critical)

## Low Risk issues

### [LR-01] Initializer not disabled on implementation contract 

**Summary**: the [PirexRewards](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L15) contract is deployed as a transparent upgradeable proxy with an initializer function that is correctly implemented, which means that if the deployment is executed correctly the initialize function is called within the same transaction to setup the instance and cannot be called anymore. However, the initialize method is not disabled on the logic contract, allowing an attacker to execute it.

**Impact**: while offering an attack surface that could lead to successfull attacks in the future implementations, the current PirexRewards implementation does not contain delegateCalls, no owner provided values that can be set in such a way to cause the logic contract to self destruct, and the upgrade structure is not controlled by the implementation.

**Recommended Mitigation Steps**: call \_disableInitializers() (see [Initializing the Implementation Contract](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#initializing_the_implementation_contract)) in the PirexRewards constructor to disable the initialization of the implementation/logic contract.

### [LR-02] PirexERC4626's beforeDeposit callback always receiveing one uninitialized parameter

**Summary**: the third argument ([shares](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L65)) of the [beforeDeposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L65) call in the [deposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L60) function is always zero and the second argument ([assets](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L85)) of the [beforeDeposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L85) call in the [mint](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L80) function is always zero.

In both cases this is caused by the use of named return variables ([shares](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L63) and [assets](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L83)) as an argument to the beforeDeposit() call.

**Impact**: currently these two issues cause no harm or errors since the implementations do not use the parameters, but being a base ERC4626 implementation it may introduce bugs in future work if it goes undetected.

**Recommended Mitigation Steps**: Move beforeDeposit calls after the initialization of the named return variable used as argument to the call.

```solidity
diff --git a/src/vaults/PirexERC4626.sol b/src/vaults/PirexERC4626.sol
index 90c4493..abd6f42 100644
--- a/src/vaults/PirexERC4626.sol
+++ b/src/vaults/PirexERC4626.sol
@@ -62,11 +62,11 @@ abstract contract PirexERC4626 is ERC20 {
         virtual
         returns (uint256 shares)
     {
-        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
-
         // Check for rounding error since we round down in previewDeposit.
         require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

+        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
+
         // Need to transfer before minting or ERC777s could reenter.
         asset.safeTransferFrom(msg.sender, address(this), assets);

@@ -82,10 +82,10 @@ abstract contract PirexERC4626 is ERC20 {
         virtual
         returns (uint256 assets)
     {
-        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
-
         assets = previewMint(shares); // No need to check for rounding error, previewMint rounds up.

+        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
+
         // Need to transfer before minting or ERC777s could reenter.
         asset.safeTransferFrom(msg.sender, address(this), assets);
```

### [LR-03] Staked Gmx RewardTracker may retain allowances

**Summary**: Both the [configureGmxState()](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272) function and the [setContract(Contracts c, address contractAddress)](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L313) can be used to update the [PirexGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L20) contract's [stakedGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L72) storage variable with a new staked Gmx RewardTracker contract. However only the [setContract(Contracts c, address contractAddress)](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L313) reset and set the allowance for the PirexContract before updating the [stakedGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L72) storage variable with the new contract.

**Impact**: an older Staked Gmx contract (eventually a vulnerable one) may retain unlimited allowance to spend Gmx from [PirexGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L20) contract if inadvertently updated via the [configureGmxState()](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272) function rather than the [setContract(Contracts c, address contractAddress)](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L313) one. 

**Recommended Mitigation Steps**: if the intent of the [configureGmxState()](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272) function is to initialize/configure the [PirexGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L20) contract once and only once, and use the [setContract(Contracts c, address contractAddress)](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L313) for live updates, use a flag to prevent further calls to the [configureGmxState()](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272) function after the first one.

## Non Critical issues

### [NC-01] External concerns in PirexERC4626

**Summary**: The [PirexERC4626](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/PirexERC4626.sol) class is based on solmate's ERC4626 contract with addition of few callbacks, like [beforeDeposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L278). 
A logical condition requires that the pure virtual [totalAssets()](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L154) function return a non-zero value for the callback to be called which is not a concern [beforeDeposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L278) should deal with at this level. The only condition for a beforeDeposit() callback is that there is a deposit/mint call, no exception.

**Impact**: [PirexERC4626](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/PirexERC4626.sol) may not be reusable, no fit or cause bugs in future use cases, require changes in multiple contracts.

**Recommended Mitigation Steps**: Remove the logical condition `if (totalAssets() != 0)` before the beforeDeposit() calls [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L65) and [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L85) so that [beforeDeposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L278) is always called, and handle the specifics of this higher logic in the specific implementation.

In specific [AutoPxGlp](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol) and [AutoPxGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol) contracts inherits [PirexERC4626](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/PirexERC4626.sol) and implement the virtual function [beforeDeposit](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L278) to compound ([#1](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L467), [#2](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol#L227)) just before the deposit happens. It is in these classes that the check should be done because it is compound function's requirement and concern to have some assets to compound on, not ERC4626's base abstract class. This is also impacting:
- [AutoPxGlp](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol) where instead of having the sole [beforeDeposit()](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L462) callback dealing with the non-zero asset concern before calling compound, i.e. "if (totalAssets() != 0) [compound(1, 1, true);](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L467)", there is
	- [depositFsGlp()](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L330) repeating [if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L338)
	- [depositGlp()](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L367) repeating [if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L380)
	- [depositGlpETH](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L413) repeating [if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGlp.sol#L423)
- [AutoPxGmx](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol) where instead of having the sole [beforeDeposit()](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol#L222) callback implementation dealing with the non-zero asset concern before calling compound, i.e. "if (totalAssets() != 0) [compound(poolFee, 1, 0, true);](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol#L227)", there is
	- [depositGmx()](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol#L370) repeating [if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);](https://github.com/code-423n4/2022-11-redactedcartel/blob/9e9bb60f117334da7c5d851646a168ca271575fc/src/vaults/AutoPxGmx.sol#L379)

Below a diff to apply the changes described.

```solidity
diff --git a/src/vaults/AutoPxGlp.sol b/src/vaults/AutoPxGlp.sol
index 15253e0..e56985e 100644
--- a/src/vaults/AutoPxGlp.sol
+++ b/src/vaults/AutoPxGlp.sol
@@ -335,7 +335,7 @@ contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
         if (amount == 0) revert ZeroAmount();
         if (receiver == address(0)) revert ZeroAddress();
 
-        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
+        beforeDeposit(address(0), 0, 0);
 
         ERC20 stakedGlp = ERC20(address(PirexGmx(platform).stakedGlp()));
 
@@ -377,7 +377,7 @@ contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
         if (minGlp == 0) revert ZeroAmount();
         if (receiver == address(0)) revert ZeroAddress();
 
-        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
+        beforeDeposit(address(0), 0, 0);
 
         // PirexGmx will do the check whether the token is whitelisted or not
         ERC20 erc20Token = ERC20(token);
@@ -420,7 +420,7 @@ contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
         if (minGlp == 0) revert ZeroAmount();
         if (receiver == address(0)) revert ZeroAddress();
 
-        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
+        beforeDeposit(address(0), 0, 0);
 
         (, uint256 assets, ) = PirexGmx(platform).depositGlpETH{
             value: msg.value
@@ -464,7 +464,7 @@ contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
         uint256,
         uint256
     ) internal override {
-        compound(1, 1, true);
+        if (totalAssets() != 0) compound(1, 1, true);
     }
 
     /**
diff --git a/src/vaults/AutoPxGmx.sol b/src/vaults/AutoPxGmx.sol
index 69d1b85..3e2d61b 100644
--- a/src/vaults/AutoPxGmx.sol
+++ b/src/vaults/AutoPxGmx.sol
@@ -224,7 +224,7 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
         uint256,
         uint256
     ) internal override {
-        compound(poolFee, 1, 0, true);
+        if (totalAssets() != 0) compound(poolFee, 1, 0, true);
     }
 
     /**
@@ -376,7 +376,7 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
         if (receiver == address(0)) revert ZeroAddress();
 
         // Handle compounding of rewards before deposit (arguments are not used by `beforeDeposit` hook)
-        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
+        beforeDeposit(address(0), 0, 0);
 
         // Intake sender GMX
         gmx.safeTransferFrom(msg.sender, address(this), amount);
diff --git a/src/vaults/PirexERC4626.sol b/src/vaults/PirexERC4626.sol
index 90c4493..3d6c3e2 100644
--- a/src/vaults/PirexERC4626.sol
+++ b/src/vaults/PirexERC4626.sol
@@ -62,7 +62,7 @@ abstract contract PirexERC4626 is ERC20 {
         virtual
         returns (uint256 shares)
     {
-        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
+        beforeDeposit(receiver, assets, shares);
 
         // Check for rounding error since we round down in previewDeposit.
         require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
@@ -82,7 +82,7 @@ abstract contract PirexERC4626 is ERC20 {
         virtual
         returns (uint256 assets)
     {
-        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
+        beforeDeposit(receiver, assets, shares);
 
         assets = previewMint(shares); // No need to check for rounding error, previewMint rounds up.

```
