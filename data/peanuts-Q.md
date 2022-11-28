##Table of Contents

- [L-01] Require() should be used instead of assert()
- [N-01] Avoid the use of sensitive terms in favour of neutral ones
- [N-02] Event is missing indexed fields

### [L-01] Require() should be used instead of assert()


Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert()do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225

### [N-01] Avoid the use of sensitive terms in favour of neutral ones

Use allowlist rather than whitelist

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L359-L389

### [N-02] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L86-L138
