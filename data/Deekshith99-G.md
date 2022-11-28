## [G-1] X = X + Y IS CHEAPER THAN X += Y;
    I found 1 instance of it at (attached permalink of it)
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L95
reference link - https://code4rena.com/reports/2022-08-olympus#g-11-x--x--y-is-cheaper-than-x--y-6-instances


## [G-2] State variables can be packed in fewer storage slots
here is the permalink of it (uint24 can be placed in between two address it can potentially save one storage slot)
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L26


