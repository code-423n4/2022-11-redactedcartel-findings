# The Return Values of compound( ) are not used

Referenced Code: 

AutoPxGlp.sol:  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol

AutoPxGmx.sol: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol

# All Funds will be locked in AutoPxGlp.sol and Rewards will be unclaimable in PirexRewards.sol in ~83 years

In both _userAccrue( ) and _globalAccrue( ), block.timestamp is converted to a uint32 via solmate's SafeCast library.  However, block.timestamp will be greater than uint32 in ~83 years, which very well could be within our lifetimes.  At this point, block.timestamp.safeCastTo32( ) will revert.  
Given that AutoPxGlp.sol uses _userAccrue( ) and _globalAccrue( ) in afterDeposit( ), afterWithdraw( ), and afterTransfer( ), all funds in the contract will be frozen and no new funds will able to be deposited.  All rewards in PirexRewards.sol( ) will not be able to be claimed.

Referenced Code:  

PirexRewards.sol: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

SafeCastLib.sol:  https://github.com/transmissions11/solmate/blob/main/src/utils/SafeCastLib.sol

