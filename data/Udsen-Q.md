## State variable declaration order can be optimized to ***save one storage slot***

Can reorder the state variable declaration to save storage slots

    uint24 public poolFee = 3000;

    uint256 public withdrawalPenalty = 300;
    uint256 public platformFee = 1000;
    uint256 public compoundIncentive = 1000;
    address public platform;

poolFee state variable can be declared after the platform variable which uses 3 bytes and 20 bytes respectively.
This will save one storage slot. 

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L26