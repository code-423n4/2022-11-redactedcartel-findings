## On the contract `AutoPxGmx.sol` & `AutoPxGLP` the functions `redeem` and `withdraw` allow assets tokens to be sent to `0x0` address, essentially being burned (sent to an address that they can't be recovered from). This may be an intended feature.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L315-L337

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L339-L362