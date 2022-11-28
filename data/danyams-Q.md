# The Return Values of compound( ) are not used

Referenced Code: 

AutoPxGlp.sol:  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol

AutoPxGmx.sol: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol

# UserState and GlobalState both use uint32

In both _userAccrue( ) and _globalAccrue( ), block.timestamp is converted to a uint32 via solmate's SafeCast library.  However, block.timestamp will be greater than uint32 in ~83 years, which very well could be within our lifetimes.