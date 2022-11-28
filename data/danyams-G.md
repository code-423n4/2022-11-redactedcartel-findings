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