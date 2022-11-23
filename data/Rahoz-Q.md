## L. Should use safeDecreaseAllowance instead of approve to zero
When call PirexGmx.setContract, if c == Contracts.StakedGmx then allowance to gmx will be set to 0
As suggest from Openzeppelin, we should use safeDecreaseAllowance to prevent front-run possible
### Proof of Concept
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L348
### Recommended Mitigation Steps
Should use safeDecreaseAllowance 

## N. FUNCTION ORDER
Functions should be ordered following the Solidity conventions.
## Proof of Concept
PirexGmx.sol
PxGmxReward.sol
AutoPxGmx.sol
AutoPxGlp.sol
PirexRewards.sol

## N. MISSING NATSPEC
Code should include NatSpec
## Proof of Concept
AutoPxGmx.withdraw
AutoPxGmx.redeem
PirexERC4626.deposit
PirexERC4626.mint
PirexERC4626.withdraw
PirexERC4626.redeem
PirexERC4626.convertToShares
PirexERC4626.previewDeposit
PirexERC4626.previewWithdraw
PirexERC4626.previewRedeem
PirexERC4626.maxDeposit
PirexERC4626.maxWithdraw
PirexERC4626.maxMint
PirexERC4626.maxRedeem