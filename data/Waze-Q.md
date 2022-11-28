## [L-1] Missing Zero Address Check
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L100

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.
## Recommendation
Consider adding explicit zero-address validation on input parameters of address type.

## [L-2] Missing zero validation on constructor 

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxGmx.sol#L10

Checking addresses against zero-address during initialization in constructor is a security best-practice. However, such checks are missing in multiple constructors.  Allowing zero-addresses will lead to contract reverts and force redeployments if there are no setters for such address variables. 
## Recommendation
Consider to Add zero-address checks in the constructors.

## [N-1] Missing indexed fields

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L33
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L92
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L96
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L140
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L141
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L143

Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.
## Recommendation
Consider adding the indexed keyword to event parameters that should include it.

## [N-2] Naming convention of function and variables.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L28

The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the mixedCase format unless variables are declared as constant in which case they utilize the UPPER_CASE_WITH_UNDERSCORES format. Private variables and functions should lead with an _underscore.
## Recommendation:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the Solidity style guide.

