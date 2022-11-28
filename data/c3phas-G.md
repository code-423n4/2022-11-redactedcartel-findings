## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Using immutable on variables that are only set in the constructor and never after 
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L15
```solidity
File: /src/vaults/PxGmxReward.sol
15:    ERC20 public pxGmx;
```

```diff
diff --git a/src/vaults/PxGmxReward.sol b/src/vaults/PxGmxReward.sol
index ec892e7..9c540b3 100644
--- a/src/vaults/PxGmxReward.sol
+++ b/src/vaults/PxGmxReward.sol
@@ -12,7 +12,7 @@ contract PxGmxReward is Owned {
     using SafeTransferLib for ERC20;
     using SafeCastLib for uint256;

-    ERC20 public pxGmx;
+    ERC20 public immutable pxGmx;

     GlobalState public globalState;
     uint256 public rewardState;
```


### Tighly pack storage variables/optimize the order of variable declaration

Here, the storage variables can be tightly packed from:

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L25-L31
### We can save 1 SLOT here. pack poolFee and platform together.
```solidity
File: /src/vaults/AutoPxGmx.sol

//@audit: Move uint24 public poolFee next to  address public platform as they can be packed together
25:    // Uniswap pool fee
26:    uint24 public poolFee = 3000;

28:    uint256 public withdrawalPenalty = 300;
29:    uint256 public platformFee = 1000;
30:    uint256 public compoundIncentive = 1000;
31:    address public platform;
```

```diff
diff --git a/src/vaults/AutoPxGmx.sol b/src/vaults/AutoPxGmx.sol
index 69d1b85..0466629 100644
--- a/src/vaults/AutoPxGmx.sol
+++ b/src/vaults/AutoPxGmx.sol
@@ -22,12 +22,13 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
     uint256 public constant FEE_DENOMINATOR = 10000;
     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

-    // Uniswap pool fee
-    uint24 public poolFee = 3000;
+

     uint256 public withdrawalPenalty = 300;
     uint256 public platformFee = 1000;
     uint256 public compoundIncentive = 1000;
+        // Uniswap pool fee
+    uint24 public poolFee = 3000;
     address public platform;

     // Address of the rewards module (ie. PirexRewards instance)
```

### The result of a function call should be cached rather than re-calling the function
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L370-L403
### AutoPxGmx.sol.depositGmx(): Result of totalAssets() should be cached rather than call it twice
```solidity
File: /src/vaults/AutoPxGmx.sol
370:    function depositGmx(uint256 amount, address receiver)

379:        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);

381:        // Intake sender GMX
382:        gmx.safeTransferFrom(msg.sender, address(this), amount);

394:        if (
395:            (shares = supply == 0
396:                ? assets
397:                : assets.mulDivDown(supply, totalAssets() - assets)) == 0
398:        ) revert ZeroShares();
```

```diff
diff --git a/src/vaults/AutoPxGmx.sol b/src/vaults/AutoPxGmx.sol
index 69d1b85..1c55d24 100644
--- a/src/vaults/AutoPxGmx.sol
+++ b/src/vaults/AutoPxGmx.sol
@@ -376,7 +376,8 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
         if (receiver == address(0)) revert ZeroAddress();

         // Handle compounding of rewards before deposit (arguments are not used by `beforeDeposit` hook)
-        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
+        uint256 _totalAssets = totalAssets();
+        if (_totalAssets != 0) beforeDeposit(address(0), 0, 0);

         // Intake sender GMX
         gmx.safeTransferFrom(msg.sender, address(this), amount);
@@ -394,7 +395,7 @@ contract AutoPxGmx is ReentrancyGuard, Owned, PirexERC4626 {
         if (
             (shares = supply == 0
                 ? assets
-                : assets.mulDivDown(supply, totalAssets() - assets)) == 0
+                : assets.mulDivDown(supply, _totalAssets - assets)) == 0
         ) revert ZeroShares();

         _mint(receiver, shares);
```


### Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L216-L218
```solidity
File: /src/PirexRewards.sol
216:       UserState memory userState = producerTokens[producerToken].userStates[
217:            user
218:        ];
```

```diff
diff --git a/src/PirexRewards.sol b/src/PirexRewards.sol
index 59c5ff7..76874de 100644
--- a/src/PirexRewards.sol
+++ b/src/PirexRewards.sol
@@ -213,7 +213,7 @@ contract PirexRewards is OwnableUpgradeable {
             uint256 rewards
         )
     {
-        UserState memory userState = producerTokens[producerToken].userStates[
+        UserState storage userState = producerTokens[producerToken].userStates[
             user
         ];
```

A similar suggestion to the above has already been implemented on [Line 285](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L285)
```solidity
285:        UserState storage u = producerTokens[producerToken].userStates[user];
```

### x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L95
```solidity
File: /src/vaults/PxGmxReward.sol
95:            rewardState += rewardAmount;
```

```diff
diff --git a/src/vaults/PxGmxReward.sol b/src/vaults/PxGmxReward.sol
index ec892e7..a52f749 100644
--- a/src/vaults/PxGmxReward.sol
+++ b/src/vaults/PxGmxReward.sol
@@ -92,7 +92,7 @@ contract PxGmxReward is Owned {
         _globalAccrue();

         if (rewardAmount != 0) {
-            rewardState += rewardAmount;
+            rewardState = rewardState + rewardAmount;

             emit Harvest(rewardAmount);
         }
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L104
```solidity
File: /src/vaults/AutoPxGmx.sol

//@audit: uint24 _poolFee
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

//@audit: uint24 fee,uint160 sqrtPriceLimitX96
242:    function compound(
243:        uint24 fee,
244:        uint256 amountOutMinimum,
245:        uint160 sqrtPriceLimitX96,
246:        bool optOutIncentive
247:    )
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L83
```solidity
File: /src/PirexFees.sol

//@audit: uint8 _treasuryFeePercent
83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)
```

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L163-L167
```solidity
File: /src/PirexRewards.sol
163:        for (uint256 i; i < len; ++i) {
164:            if (address(rewardTokens[i]) == address(rewardToken)) {
165:                revert TokenAlreadyAdded();
166:            }
167:        }
```

```diff
diff --git a/src/PirexRewards.sol b/src/PirexRewards.sol
index 59c5ff7..41da35e 100644
--- a/src/PirexRewards.sol
+++ b/src/PirexRewards.sol
@@ -160,10 +160,13 @@ contract PirexRewards is OwnableUpgradeable {
         ERC20[] memory rewardTokens = p.rewardTokens;
         uint256 len = rewardTokens.length;

-        for (uint256 i; i < len; ++i) {
+        for (uint256 i; i < len;) {
             if (address(rewardTokens[i]) == address(rewardToken)) {
                 revert TokenAlreadyAdded();
             }
+            unchecked {
+                ++i;
+            }
         }
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L351-L363
```solidity
File:/src/PirexRewards.sol
351:        for (uint256 i; i < pLen; ++i) {

363:        }
```

```diff
diff --git a/src/PirexRewards.sol b/src/PirexRewards.sol
index 59c5ff7..2fc8bb9 100644
--- a/src/PirexRewards.sol
+++ b/src/PirexRewards.sol
@@ -348,7 +348,7 @@ contract PirexRewards is OwnableUpgradeable {
         uint256 pLen = _producerTokens.length;

         // Iterate over the producer tokens and update reward state
-        for (uint256 i; i < pLen; ++i) {
+        for (uint256 i; i < pLen;) {
             ERC20 p = _producerTokens[i];
             uint256 r = rewardAmounts[i];

@@ -360,6 +360,9 @@ contract PirexRewards is OwnableUpgradeable {
             if (r != 0) {
                 producerState.rewardStates[rewardTokens[i]] += r;
             }
+            unchecked {
+                ++i;
+            }
         }

```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L396-L415
```solidity
File: /src/PirexRewards.sol
396:            for (uint256 i; i < rLen; ++i) {

415:            }
```

```diff
diff --git a/src/PirexRewards.sol b/src/PirexRewards.sol
index 59c5ff7..56e5d91 100644
--- a/src/PirexRewards.sol
+++ b/src/PirexRewards.sol
@@ -393,7 +393,7 @@ contract PirexRewards is OwnableUpgradeable {
             emit Claim(producerToken, user);

             // Transfer the proportionate reward token amounts to the recipient
-            for (uint256 i; i < rLen; ++i) {
+            for (uint256 i; i < rLen;) {
                 ERC20 rewardToken = rewardTokens[i];
                 address rewardRecipient = p.rewardRecipients[user][rewardToken];
                 address recipient = rewardRecipient != address(0)
@@ -412,6 +412,9 @@ contract PirexRewards is OwnableUpgradeable {
                         recipient
                     );
                 }
+                unchecked {
+                    ++i;
+                }
             }
         }
```
[see resource](https://github.com/ethereum/solidity/issues/10695)

### Functions guaranteed to revert when called by normal users can be marked `payable` save around 20 gas per function call

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.  

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L63-L66
```solidity
File:/src/PirexFees.sol
63:    function setFeeRecipient(FeeRecipient f, address recipient)
64:        external
65:        onlyOwner
66:    {


83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)
84:        external
85:        onlyOwner
96:    {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L104
```solidity
File: /src/vaults/AutoPxGmx.sol
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:    function setPlatformFee(uint256 fee) external onlyOwner {

140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:    function setPlatform(address _platform) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L94
```solidity
File: /src/vaults/AutoPxGlp.sol
94:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

106:    function setPlatformFee(uint256 fee) external onlyOwner {

118:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

130:    function setPlatform(address _platform) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L93
```solidity
File: /src/PirexRewards.sol
93:    function setProducer(address _producer) external onlyOwner {

151:    function addRewardToken(ERC20 producerToken, ERC20 rewardToken)
152:        external
153:        onlyOwner
154:    {

179:    function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
180:        external
181:        onlyOwner
182:    {

432:    function setRewardRecipientPrivileged(
433:        address lpContract,
434:        ERC20 producerToken,
435:        ERC20 rewardToken,
436:        address recipient
437:    ) external onlyOwner {

461:    function unsetRewardRecipientPrivileged(
462:        address lpContract,
463:        ERC20 producerToken,
464:        ERC20 rewardToken
465:    ) external onlyOwner {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L300
```solidity
File: /src/PirexGmx.sol
300:    function setFee(Fees f, uint256 fee) external onlyOwner {

313:    function setContract(Contracts c, address contractAddress)
314:        external
315:        onlyOwner
316:    {

862:    function setDelegationSpace(
863:        string memory _delegationSpace,
864:        bool shouldClear
865:    ) external onlyOwner {

884:    function setVoteDelegate(address voteDelegate) external onlyOwner {

895:    function clearVoteDelegate() public onlyOwner {

909:    function setPauseState(bool state) external onlyOwner {

921:    function initiateMigration(address newContract)
922:        external
923:        whenPaused
924:        onlyOwner
925:    {


956:    function completeMigration(address oldContract)
957:        external
958:        whenPaused
959:        onlyOwner
960:    {
```

## The following should have been caught by the automated script but for some reasons they were not. Adding for completeness

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272-L295
### PirexGmx.sol.configureGmxState(): gmxRewardRouterV2 should be cached
```solidity
File: /src/PirexGmx.sol
272:    function configureGmxState() external onlyOwner whenPaused {

274:        rewardTrackerGmx = RewardTracker(gmxRewardRouterV2.feeGmxTracker());
275:        rewardTrackerGlp = RewardTracker(gmxRewardRouterV2.feeGlpTracker());
276:        feeStakedGlp = RewardTracker(gmxRewardRouterV2.stakedGlpTracker());
277:        stakedGmx = RewardTracker(gmxRewardRouterV2.stakedGmxTracker());
278:        glpManager = gmxRewardRouterV2.glpManager();
279:        gmxVault = IVault(IGlpManager(glpManager).vault());
```

```diff
diff --git a/src/PirexGmx.sol b/src/PirexGmx.sol
index e0034a1..0ff0bef 100644
--- a/src/PirexGmx.sol
+++ b/src/PirexGmx.sol
@@ -271,11 +271,12 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
      */
     function configureGmxState() external onlyOwner whenPaused {
         // Variables which can be assigned by reading previously-set GMX contracts
-        rewardTrackerGmx = RewardTracker(gmxRewardRouterV2.feeGmxTracker());
-        rewardTrackerGlp = RewardTracker(gmxRewardRouterV2.feeGlpTracker());
-        feeStakedGlp = RewardTracker(gmxRewardRouterV2.stakedGlpTracker());
-        stakedGmx = RewardTracker(gmxRewardRouterV2.stakedGmxTracker());
-        glpManager = gmxRewardRouterV2.glpManager();
+        IRewardRouterV2  _gmxRewardRouterV2 = gmxRewardRouterV2;
+        rewardTrackerGmx = RewardTracker(_gmxRewardRouterV2.feeGmxTracker());
+        rewardTrackerGlp = RewardTracker(_gmxRewardRouterV2.feeGlpTracker());
+        feeStakedGlp = RewardTracker(_gmxRewardRouterV2.stakedGlpTracker());
+        stakedGmx = RewardTracker(_gmxRewardRouterV2.stakedGmxTracker());
+        glpManager = _gmxRewardRouterV2.glpManager();
         gmxVault = IVault(IGlpManager(glpManager).vault());
```


https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272-L295
### PirexGmx.sol.configureGmxState(): glpManager should be cached.
```solidity
File: /src/PirexGmx.sol
272:    function configureGmxState() external onlyOwner whenPaused {

279:        gmxVault = IVault(IGlpManager(glpManager).vault());

281:        emit ConfigureGmxState(
282:            msg.sender,
283:            rewardTrackerGmx,
284:            rewardTrackerGlp,
285:            feeStakedGlp,
286:            stakedGmx,
287:            glpManager,
288:            gmxVault
289:        );
```

```diff
diff --git a/src/PirexGmx.sol b/src/PirexGmx.sol
index e0034a1..06a1e84 100644
--- a/src/PirexGmx.sol
+++ b/src/PirexGmx.sol
@@ -276,7 +276,8 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
         feeStakedGlp = RewardTracker(gmxRewardRouterV2.stakedGlpTracker());
         stakedGmx = RewardTracker(gmxRewardRouterV2.stakedGmxTracker());
         glpManager = gmxRewardRouterV2.glpManager();
-        gmxVault = IVault(IGlpManager(glpManager).vault());
+        address  _glpManager = glpManager;
+        gmxVault = IVault(IGlpManager(_glpManager).vault());

         emit ConfigureGmxState(
             msg.sender,
@@ -284,7 +285,7 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
             rewardTrackerGlp,
             feeStakedGlp,
             stakedGmx,
-            glpManager,
+            _glpManager,
             gmxVault
         );
```


https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L272-L295
```solidity
File: /src/PirexGmx.sol
272:    function configureGmxState() external onlyOwner whenPaused {

277:        stakedGmx = RewardTracker(gmxRewardRouterV2.stakedGmxTracker());

281:        emit ConfigureGmxState(
282:            msg.sender,
283:            rewardTrackerGmx,
284:            rewardTrackerGlp,
285:            feeStakedGlp,
286:            stakedGmx,
287:            glpManager,
288:            gmxVault
289:        );

292:        gmx.safeApprove(address(stakedGmx), type(uint256).max);
293:    }
```

```diff
diff --git a/src/PirexGmx.sol b/src/PirexGmx.sol
index e0034a1..4035b4a 100644
--- a/src/PirexGmx.sol
+++ b/src/PirexGmx.sol
@@ -278,18 +278,20 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
         glpManager = gmxRewardRouterV2.glpManager();
         gmxVault = IVault(IGlpManager(glpManager).vault());

+        RewardTracker _stakedGmx = stakedGmx;
+
         emit ConfigureGmxState(
             msg.sender,
             rewardTrackerGmx,
             rewardTrackerGlp,
             feeStakedGlp,
-            stakedGmx,
+            _stakedGmx,
             glpManager,
             gmxVault
         );

         // Approve GMX to enable staking
-        gmx.safeApprove(address(stakedGmx), type(uint256).max);
+        gmx.safeApprove(address(_stakedGmx), type(uint256).max);
     }
```


https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L422-L465
```solidity
File: /src/PirexGmx.sol
422:    function depositFsGlp(uint256 amount, address receiver)

436:        stakedGlp.transferFrom(msg.sender, address(this), amount);

452:        emit DepositGlp(
453:            msg.sender,
454:            receiver,
455:            address(stakedGlp),
                ...
462:        );
```

```diff
diff --git a/src/PirexGmx.sol b/src/PirexGmx.sol
index e0034a1..78f5324 100644
--- a/src/PirexGmx.sol
+++ b/src/PirexGmx.sol
@@ -433,7 +433,8 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
         if (receiver == address(0)) revert ZeroAddress();

         // Transfer the caller's fsGLP (unstaked for the user, staked for this contract)
-        stakedGlp.transferFrom(msg.sender, address(this), amount);
+        IStakedGlp  _stakedGlp = stakedGlp;
+        _stakedGlp.transferFrom(msg.sender, address(this), amount);

         // Get the pxGLP amounts for the receiver and the protocol (fees)
         (uint256 postFeeAmount, uint256 feeAmount) = _computeAssetAmounts(
@@ -452,7 +453,7 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
         emit DepositGlp(
             msg.sender,
             receiver,
-            address(stakedGlp),
+            address(_stakedGlp),
             0,
             0,
             0,
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L739-L816
### PirexGmx.sol.claimRewards(): stakedGmx should be cached
```solidity
File: /src/PirexGmx.sol
739:    function claimRewards()
    
763:        uint256 esGmxBeforeClaim = stakedGmx.depositBalances(
764:            address(this),
765:            address(esGmx)
766:        );

787:        uint256 esGmxRewards = stakedGmx.depositBalances(
788:            address(this),
789:            address(esGmx)
790:        ) - esGmxBeforeClaim;
```

```diff
diff --git a/src/PirexGmx.sol b/src/PirexGmx.sol
index e0034a1..d3e8754 100644
--- a/src/PirexGmx.sol
+++ b/src/PirexGmx.sol
@@ -760,7 +760,8 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {

         // Get pre-reward claim reward token balances to calculate actual amount received
         uint256 baseRewardBeforeClaim = gmxBaseReward.balanceOf(address(this));
-        uint256 esGmxBeforeClaim = stakedGmx.depositBalances(
+        RewardTracker _stakedGmx = stakedGmx;
+        uint256 esGmxBeforeClaim = _stakedGmx.depositBalances(
             address(this),
             address(esGmx)
         );
@@ -784,7 +785,7 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {

         uint256 baseRewards = gmxBaseReward.balanceOf(address(this)) -
             baseRewardBeforeClaim;
-        uint256 esGmxRewards = stakedGmx.depositBalances(
+        uint256 esGmxRewards = _stakedGmx.depositBalances(
             address(this),
             address(esGmx)
         ) - esGmxBeforeClaim;
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L940-L950
### PirexGmx.sol.migrateReward(): migratedTo should be cached(Happy path)
```solidity
File: /src/PirexGmx.sol
940:    function migrateReward() external whenPaused {
941:        if (msg.sender != migratedTo) revert NotMigratedTo();

946:        gmxBaseReward.safeTransfer(
947:            migratedTo,
948:            gmxBaseReward.balanceOf(address(this))
949:        );
950:    }
```

```diff
diff --git a/src/PirexGmx.sol b/src/PirexGmx.sol
index e0034a1..e7b4a1c 100644
--- a/src/PirexGmx.sol
+++ b/src/PirexGmx.sol
@@ -938,13 +938,14 @@ contract PirexGmx is ReentrancyGuard, Owned, Pausable {
         @notice Migrate remaining (base) reward to the new contract after completing migration
     */
     function migrateReward() external whenPaused {
-        if (msg.sender != migratedTo) revert NotMigratedTo();
+        address _migratedTo = migratedTo;
+        if (msg.sender != _migratedTo) revert NotMigratedTo();
         if (gmxRewardRouterV2.pendingReceivers(address(this)) != address(0))
             revert PendingMigration();

         // Transfer out any remaining base reward (ie. WETH) to the new contract
         gmxBaseReward.safeTransfer(
-            migratedTo,
+            _migratedTo,
             gmxBaseReward.balanceOf(address(this))
         );
     }
```
