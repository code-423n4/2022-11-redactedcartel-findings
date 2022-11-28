# [L] USE TWO-PHASE OWNERSHIP TRANSFERS

Some of contracts use `solmate/auth/Owned.sol`, and this implementation doesn't have an accept function after transfer ownership being executed.

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call accept() to become the new owner. This prevents passing the ownership to an account that is unable to use it.

Affected contracts:
- `PirexFees.sol`
- `PirexGmx.sol`
- `AutoPxGmx.sol`
- `PxGmxReward`

# [L] REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

instances:
```solidity
File: PirexGmx.sol
225:         assert(feeAmount + postFeeAmount == assets);
```