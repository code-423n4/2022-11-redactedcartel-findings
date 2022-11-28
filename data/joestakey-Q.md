## Summary
### Low Risk
|      | Issue                                                                                 |
|------|---------------------------------------------------------------------------------------|
| L-01 | Wrong comment                                                                         |
| L-02 | `AutoPxGlp.setPlatform` and `AutoPxGmx.setPlatform` break the vaults functionalities. |
| L-03 | Anyone can claim rewards with `PirexRewards.claim()` on behalf on anyone              |
| L-04 | Use custom errors to make error handling easier                                       |
| L-05 | Use `require` instead of `assert`                                                     |
| L-06 | `claimUserReward` should not emit events if no rewards have been claimed              |


### Non-critical
|      | Issue                                                                                 |
|------|---------------------------------------------------------------------------------------|
| N-01 | Contract implements interface without extending the interface                         |


## Low
### [L‑01] Wrong comment

```solidity
86:         // Approve the Uniswap V3 router to manage our base reward (inbound swap token) @audit - this approves `pirexGMX`, not the router.
87:         gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L86

### [L‑02] `AutoPxGlp.setPlatform` and `AutoPxGmx.setPlatform` break the vaults functionalities.

Looking at `AutoPxGlp.setPlatform`: this admin setter allows the owner to change the `pirexGmx` address in `AutoPxGlp`. The issue is that it does not call `gmxBaseReward.safeApprove(platform, type(uin256).max)`.

This means that users will not be able to call `depositFsGlp()` anymore, as it calls `beforeDeposit()`, which calls `compound()`, where a `platform.depositGlp()` call is made:

```solidity
238:         if (gmxBaseRewardAmountIn != 0) {
239:             // Deposit received rewards for pxGLP
240:             (, pxGlpAmountOut, ) = PirexGmx(platform).depositGlp(
241:                 address(gmxBaseReward),
242:                 gmxBaseRewardAmountIn,
243:                 minUsdg,
244:                 minGlp,
245:                 address(this)
246:             );
247:         }
```

This external call will revert [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L506) as the `platform`'s `gmxBaseReward` allowance is `0`.

Users will not only lose the ability to `deposit` `GLP` into the contracts, they also will not be able to call `withdraw` or `redeem` as both these functions call `compound()`: meaning they will not be able to retrieve their `pxGLP`.

Note that while centralization issues are mentioned in the README:
```
we also believe that certain privileges need to be retained by our team in order to safeguard user funds and maintain the protocol's continued operations 
```

In this case, the issue is not that this function can be used in a way to grieve users, but that it by design simply breaks the entire `autoPxGlp` functionality: There is no way to call `gmxBaseReward.safeApprove()` on a new platform.


The exact same issue happens in `autoPxGmx` with [setPlatform](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L152), causing the `compound()` call to revert [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L281).

You should add:
- `gmxBaseReward.safeApprove(platform, type(uin256).max)` in `AutoPxGlp.setPlatform`.
- `gmx.safeApprove(platform, type(uin256).max)` in `AutoPxGmx.setPlatform`.

### [L‑03] Anyone can claim rewards with `PirexRewards.claim()` on behalf on anyone
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L373
The `PirexRewards.claim()` function allows claiming the tokens of other users.
While the tokens are sent to the correct address, this can lead to issues with smart contracts that might rely on claiming the tokens themselves.

If the contract has no other functions to transfer out funds, they may be locked forever in this contract.

Consider changing the function so that it claims for `msg.sender`.

The vaults `AutoPxGlp` and `autoPxGmx` will still work after that change, as they both do calls to `PirexRewards(rewardsModule).claim()` with `user = address(this)`

### [L‑04] Use custom errors to make error handling easier


When withdrawing `pxGMX` in `AutoPxGmx`, in case the caller has been approved by the `apxGmx` `owner`, `shares` is subtracted from the caller's allowance. 

```solidity
325:         if (msg.sender != owner) {
326:             uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
327: 
328:             if (allowed != type(uint256).max)
329:                 allowance[owner][msg.sender] = allowed - shares;
330:         }
```

The issue is that this will [revert](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L329) with an underflow error if `shares` is too high.

This might be confusing for the caller, especially in `withdraw()`, as `shares` is computed by the smart contract, and the user might expect the `assets` amount specified to correspond to a number of shares within their allowance.

Use a custom error to make error handling easier for users:

```diff
328:             if (allowed != type(uint256).max)
+                     if(allowed < shares) revert NotEnoughAllowance();
329:                 allowance[owner][msg.sender] = allowed - shares;
330:         }
```

### [L‑05] Use `require` instead of `assert`


As per Solidity's [documentation](https://docs.soliditylang.org/en/v0.8.17/control-structures.html?highlight=assert#panic-via-assert-and-error-via-require):

```
Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input
```

Change the following check in `PirexGmx`:

```diff
223:         postFeeAmount = assets - feeAmount;
224: 
-225:         assert(feeAmount + postFeeAmount == assets);
+225:         require(feeAmount + postFeeAmount == assets);
```

### [L‑06] `claimUserReward` should not emit events if no rewards have been claimed


`PirexRewards.claim` loops through the reward tokens and calls `PirexGmx.claimUserReward` to transfer the specified reward token to the receiver.

`PirexGmx.claimUserReward` emits a `ClaimUserReward()` event at the end of the call. But the issue is that this event is emitted regardless of what happened beforehand.

If the `token` is neither `pxGmx` not `WETH` (ie `gmxBaseReward`), the call will skip the [reward claiming](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L838-L848), but still emit the event.

Now, this is not a problem if the only reward tokens the contract will ever handle are `pxGmx` and `gmxBaseReward`. You can then add a check in `PirexRewards.claim` to skip the current iteration if `token != pxGmx && token != gmxBaseReward`.



## Non-critical

### [N‑01] Contract implements interface without extending the interface
Not extending the interface may lead to the wrong function signature being used, leading to unexpected behavior. If the interface is in fact being implemented, use the `override` keyword to indicate that fact

2 instances: 

```solidity
/// @audit IPirexRewards()
15:  contract PirexRewards is OwnableUpgradeable {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L15


```solidity
/// @audit IAutoPxGlp()
15:  contract AutoPxGlp is PirexERC4626, PxGmxReward, ReentrancyGuard {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L14


