## [N1] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

> FILE:  2022-11-redactedcartel/src/PirexGmx.sol
 
        function _computeAssetAmounts(Fees f, uint256 assets) 
        internal
        view
        returns (uint256 postFeeAmount, uint256 feeAmount)

 <https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L207-L214>     

<https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L339-L344>

##

## [L1] The **Fees j** parameters possible input values are only  [0,1,2]. But the parameter accept uint8 length of values . Its possible to enter wrong inputs while calling the functions.  uint8 range is 255. This is the wrong code practice. Many times the functions execution always fails . If we assign first parameter values other than [0,1,2] the function call always fails . the Fees enum size is only 2.

    > FILE:  2022-11-redactedcartel/src/PirexGmx.sol

     217:     function _computeAssetAmounts(Fees f, uint256 assets)

##

## [N2]   EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

>  While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

> There are 2 instances of this issue:

> FILE:  2022-11-redactedcartel/src/PxERC20.sol

    9:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

   10:  bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");

##

## [N3]  CONSTANT REDEFINED ELSEWHERE

>  onsider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.










