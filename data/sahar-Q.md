1. Title: used constant.
2. Details:  This defined constant is not used anywhere in the entire project.
3. GitHub Permalinks: 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L22

4. Mitigation:  Remove it.
******************************************************************************
1. Title: Using Uncheck calculation.
2. Details:  Considering that the penalty is always zero or a part of the property, as a result following subtraction, does not overflow at any condition, so it can be unchecked.
3. GitHub Permalinks: 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L168

4. Mitigation:  replace it by :  uncheck{
return assets - penalty
}
******************************************************************************
1. Title: variable packing
2. Details:  Fee is always a reasonably small value, and therefore smaller variables can be used, and since these two variables are consecutive, the method of packing variables can also be used.
3. GitHub Permalinks: 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L220

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L221

4. Mitigation:  replace by:
            Uint128 totalPxGlpFee,
            Uint128  totalPxGmxFee,



******************************************************************************
1. Title:  Future Uniswap V3 router address changes and incorrect input problem.
2. Details:
It is possible that in the future, Uniswap V3 router contract will have a problem or update to a new address, or even the contract address may be set incorrectly in this command. It seems to be logical to have a comparison command between the current address of the Uniswap V3 router contract and this input argument (address(_platform)).
 On the other hand, a mechanism to coordinate with the change of the Uniswap V3 router smart contract address should be provided.

3. GitHub Permalinks: 

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L87

4. Mitigation: 
A. Add below codes to contract:
   If(address(_platform== “0xE592427A0AEce92De3Edee1F18E0157C05861564” )
            
B. Add below code:
function changeV3Addree(address _newV3)  onlyOwner {
gmxBaseReward.safeApprove( _newV3, type(uint256).max)}

5. Difficulty+Impact: OWASP ratings:  medium (likelihood: low - impact: High) 

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L96

******************************************************************************

1. Title: Naming convention
2. Details:   Usually internal function start with “_” So it is more clear for reading and auditing. It is recommended to correct the below function's name. (NOTE:  these are override function, so The same issue should be corrected in the corresponding virtual functions.
3. GitHub Permalinks: 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L501

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L487

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L474

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L462

4. Mitigation risk:
 Replace afterTransfer  with “_afterTransfer“
Replace afterWithdraw with  “_afterWithdraw“
Replace  afterDeposit with  “_afterDeposit“
Replace beforeDeposit with  “_beforeDeposit“
    
5. Difficulty+Impact: OWASP ratings:   low (likelihood:  low  - impact: low) 
******************************************************************************

1. Title:  Missing MAX limit for pool fee
2. Details:   There is no set limit for this fee. The pool may request a large amount for the fee in cooperation with the owner and the owner will set it, and this is to the detriment of the users.
3. GitHub Permalinks: 

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L104

4. Mitigation risk:   Adding a constant uint (for example:  MAX_POOL_FEE)
And add below code:
 If (_poolFee > MAX_POOL_FEE) revert();

5. Difficulty+Impact: OWASP ratings:  medium (likelihood:  medium - impact: medium) 

******************************************************************************

1. Title: Differences in compiler versions
2. Details:   It is recommended to coordinate the compiler version of this file with other files and apply the changes required for this purpose in the PirexERC4626.sol contract uses a different version of the Solidity compiler, which is far from the compiler version in other parts of this project. In some cases, this issue causes unknown problems.
3. GitHub Permalinks: 
 https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L2

4. Mitigation risk:    
5. Difficulty+Impact: OWASP ratings:   Low (likelihood:  Low - impact: Low) 

******************************************************************************


1. Title: the wrong place for “ emit”
2. Details:  Logically, it is better that all the required events happen first, then emit happen. The same issue is correct for the second below line too.

3. GitHub Permalinks: 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L117

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L143

4. Mitigation risk:  It is recommended to move this “emit” command line after “asset.safeTransfer”  or even “afterWithdraw”.

5. Difficulty+Impact:  informational  

******************************************************************************

1. Title:    Missing partial reward claiming method.
2. Details:  In the future, it is possible that the user wants to send a part of the rewards to one address and another part to another address or a number of addresses, or the user wants to claim only a part of the rewards at the moment. In the logic used in this function, the  user (msg.sender) only has the right to claim all rewards at once and send them to only one address.
3. GitHub Permalinks:  
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L105

4. Mitigation risk :   This logic had to be added to the function that user can determine the amount of the claimed rewards in each claim function call. It is also possible to add a view function to the contract that the user can see rewards after or before claim.
5. Difficulty+Impact: OWASP ratings:  medium (likelihood:  medium - impact: medium) 


******************************************************************************

1. Title:  Unreasonable assignment
2. Details:  The purpose of the “setContract” function is only to change the address of one of the other contracts. There is no reason that for every change in the address of other contracts, the glpManager variable chenge too.
3. GitHub Permalinks: 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L367
4. Mitigation risk:  remove this line
5. Difficulty+Impact: OWASP ratings:   high (likelihood:  medium - impact:  high) 
****************************************************************************

1. Title:  Missing array length check
2. Details:   In “removeRewardToken” function, it is necessary to check whether the input variable “removalIndex” A is smaller or equal to array length or not.  This checking prevents more gas consumption before the out of range error message.
3. GitHub Permalinks:  
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L179

4. Mitigation risk:   add below code”
  if (removalIndex > rewardTokens.lengh) revert();
5. Difficulty+Impact: OWASP ratings:  medium (likelihood:  medium - impact: medium) 

****************************************************************************
1. Title:  gas saving
2. Details:   using ( _totalSupply == shares  )  is one sub operator less than (_totalSupply - shares == 0) and it has the same result. 
Same issue is correct for the second link too.

3. GitHub Permalinks:  
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L191

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L163


****************************************************************************
1. Title:  gas saving

2. Details.To optimize the gas, it is better to put the loop index in the unchecked part.

3. GitHub Permalinks:  
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L163

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L351

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L396
