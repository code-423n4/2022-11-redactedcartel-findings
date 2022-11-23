## Add unchecked {} for subtractions where the operands cannot underflow because of a previous
Because fees[f] never greater than FEE_MAX, it will make feeAmount always lower than assets.
We can add unchecked for postFeeAmount to save gas here
### Proof of Concept
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L223

## Should use memory to save sload
When update user rewards accrual state via PirexRewards.userAccrue, UserState was load from storage and it calculate `rewards` with read 3 time to storage
We should create it as memory to save 2 SLOAD
### Proof of Concept
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L285-L291
## Recommended Mitigation Steps
`UserState storage u` to `UserState memory u`

## Should approve onetime only
When user call `AutoPxGlp.depositFsGlp` / `AutoPxGlp.depositGlp` it will approve for PirexGmx before proceed with the deposit
Since there is a contract, we should consider to approve type(uint256).max in constructor, so eachtime call deposit, it dont need to approve again and again
### Proof of Concept
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L347
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L391

## Save one step transfer from sender
When user call `AutoPxGlp.depositFsGlp` it will take stakedGlp from sender and transfer to AutoPxGlp, 
then it approve for PirexGmx before transfer token again from AutoPxGlp to PirexGmx
### Proof of Concept
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L344-L352
## Recommended Mitigation Steps
We should consider to just transfer one time from sender directly to PirexGmx
