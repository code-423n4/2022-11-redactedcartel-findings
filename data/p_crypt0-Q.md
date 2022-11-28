# Informational 1: Burner privileged actors can arbitrarily burn user funds by calling burn() in PxERC20.sol
Admin with burn role can arbitrarily burn any users funds for PxERC20 based tokens by calling the burn() function inherited from PxERC20.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L62-L72

The function must be overwritten in order to avoid this functionality siphoning down to all derivatives.