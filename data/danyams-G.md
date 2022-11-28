# Increments in For Loops can go Unchecked

There are 3 different for loops in PirexRewards.sol in harvest( ), claim( ), and addRewardToken( ).  In each of these instances, the for loop will stop if the uint256, i, is equal to a uint256 that is previously assigned to the length of an array.  Since i is being compared to another uint256, there is no way for it to overflow, meaning it can go unchecked.

--- a/PirexRewards.orig.sol
+++ b/PirexRewards.sol
@@ -160,10 +160,11 @@ contract PirexRewards is OwnableUpgradeable {
         ERC20[] memory rewardTokens = p.rewardTokens;
         uint256 len = rewardTokens.length;
 
-        for (uint256 i; i < len; ++i) {
+        for (uint256 i; i < len;) {
             if (address(rewardTokens[i]) == address(rewardToken)) {
                 revert TokenAlreadyAdded();
             }
+            unchecked{++i;}
         }
 
         p.rewardTokens.push(rewardToken);
@@ -348,7 +349,7 @@ contract PirexRewards is OwnableUpgradeable {
         uint256 pLen = _producerTokens.length;
 
         // Iterate over the producer tokens and update reward state
-        for (uint256 i; i < pLen; ++i) {
+        for (uint256 i; i < pLen;) {
             ERC20 p = _producerTokens[i];
             uint256 r = rewardAmounts[i];
 
@@ -360,6 +361,7 @@ contract PirexRewards is OwnableUpgradeable {
             if (r != 0) {
                 producerState.rewardStates[rewardTokens[i]] += r;
             }
+            unchecked{++i;}
         }
 
         emit Harvest(_producerTokens, rewardTokens, rewardAmounts);
@@ -393,7 +395,7 @@ contract PirexRewards is OwnableUpgradeable {
             emit Claim(producerToken, user);
 
             // Transfer the proportionate reward token amounts to the recipient
-            for (uint256 i; i < rLen; ++i) {
+            for (uint256 i; i < rLen;) {
                 ERC20 rewardToken = rewardTokens[i];
                 address rewardRecipient = p.rewardRecipients[user][rewardToken];
                 address recipient = rewardRecipient != address(0)
@@ -412,6 +414,7 @@ contract PirexRewards is OwnableUpgradeable {
                         recipient
                     );
                 }
+                unchecked{++i;}
             }
         }
     }

Link to referenced code: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

# Changing the Allowance Can Be Set to An Internal Function

Changing the allowance of an account for an owner is repeated 5 times in total:  2 times in PirexERC4626.sol and 3 times in AutoPxGmx.sol.  In place of copying and pasting the same code, an internal function can be used which will reduce the size of the contract bytecode and, thereby, reduce the cost of deployment.  Specifically, the CODECOPY opcode costs 3 * data_size_words + mem_expansion_cost in gas.  

--- a/src/vaults/PirexERC4626.sol
+++ b/src/vaults/PirexERC4626.sol
@@ -103,13 +103,8 @@ abstract contract PirexERC4626 is ERC20 {
     ) public virtual returns (uint256 shares) {
         shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
 
-        if (msg.sender != owner) {
-            uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
-
-            if (allowed != type(uint256).max)
-                allowance[owner][msg.sender] = allowed - shares;
-        }
-
+        _updateAllowance(shares, owner);
+        
         beforeWithdraw(owner, assets, shares);
 
         _burn(owner, shares);
@@ -126,12 +121,8 @@ abstract contract PirexERC4626 is ERC20 {
         address receiver,
         address owner
     ) public virtual returns (uint256 assets) {
-        if (msg.sender != owner) {
-            uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
 
-            if (allowed != type(uint256).max)
-                allowance[owner][msg.sender] = allowed - shares;
-        }
+        _updateAllowance(shares, owner);
 
         // Check for rounding error since we round down in previewRedeem.
         require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
@@ -147,6 +138,15 @@ abstract contract PirexERC4626 is ERC20 {
         afterWithdraw(owner, assets, shares);
     }
 
+    function _updateAllowance(uint256 shares, address owner) internal {
+        if (msg.sender != owner) {
+            uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
+
+            if (allowed != type(uint256).max)
+                allowance[owner][msg.sender] = allowed - shares;
+        }
+    }
+

--- a/src/vaults/AutoPxGmx.sol
+++ b/src/vaults/AutoPxGmx.sol
@@ -322,12 +322,7 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
 
         shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
 
-        if (msg.sender != owner) {
-            uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
-
-            if (allowed != type(uint256).max)
-                allowance[owner][msg.sender] = allowed - shares;
-        }
+        _updateAllowance(shares, owner);
 
         _burn(owner, shares);
 
@@ -344,12 +339,7 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
         // Compound rewards and ensure they are properly accounted for prior to redemption calculation
         compound(poolFee, 1, 0, true);
 
-        if (msg.sender != owner) {
-            uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
-
-            if (allowed != type(uint256).max)
-                allowance[owner][msg.sender] = allowed - shares;
-        }
+        _updateAllowance(shares, owner);

Links to referenced code:

PirexERC4626.sol:  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol

AutoPxGmx.sol: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol

# Cacheing State Variables 

Certain state variables can be cached in AutoPxGlp.sol to avoid an SLOAD.  

A similar optimization can be made in compound( ) of AutoPxGlp.sol if the number of local variables can be reduced to avoid a Stack Too Deep error.  Since compound( ) will be by far the most used function of AutoPxGlp, this may be worthwhile.  

--- a/src/vaults/AutoPxGlp.sol
+++ b/src/vaults/AutoPxGlp.sol
 
@@ -337,16 +337,18 @@ contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
 
         if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
 
-        ERC20 stakedGlp = ERC20(address(PirexGmx(platform).stakedGlp()));
+        address _platform = platform; 
+        
+        ERC20 stakedGlp = ERC20(address(PirexGmx(_platform).stakedGlp()));
 
         // Transfer fsGLP from the caller to the vault
         // before approving PirexGmx to proceed with the deposit
         stakedGlp.safeTransferFrom(msg.sender, address(this), amount);
 
         // Approve as needed here since the stakedGlp address is mutable in PirexGmx
-        stakedGlp.safeApprove(platform, amount);
+        stakedGlp.safeApprove(_platform, amount);
 
-        (, uint256 assets, ) = PirexGmx(platform).depositFsGlp(
+        (, uint256 assets, ) = PirexGmx(_platform).depositFsGlp(
             amount,
             address(this)
         );

Links to Referenced Code:

AutoPxGlp.sol:  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol
AutoPxGmx.sol:  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol