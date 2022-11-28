# [L] USE TWO-PHASE OWNERSHIP TRANSFERS

Some of contracts use `solmate/auth/Owned.sol`, and this implementation doesn't have an accept function after transfer ownership being executed.

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call accept() to become the new owner. This prevents passing the ownership to an account that is unable to use it.

Affected contracts:
- `PirexFees.sol`
- `PirexGmx.sol`
- `AutoPxGmx.sol`
- `PxGmxReward`
