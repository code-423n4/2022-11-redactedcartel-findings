## `transfer` function in `PxERC20` does not check that `to` is the 0x0 address

The `burn` function on line 62 in the same contract is being overridden and is also restricted to a role. Because there is no 0x0 address, `transfer`ring the token to the 0x0 address is effectively burning the token, which goes around the intended functionality of the `burn` function.

## Steps to reproduce

Call `transfer` and make `to` the 0x0 address

### Recommendation

Add a check to prevent users from sending tokens to the 0x0 address