### [GAS-01] Function that guaranteed to revert when called by normal user can be marked payable 

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 


*Instances (25)*:
```solidity
File:         PirexFees.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L63-L77
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83-L94
```
```solidity
File:         PirexGmx.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L272-L289
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300-L306
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L313-L368
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L862-L878
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L884-L890
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L895-L899
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L909-L915
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L921-L935
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L956-L974

```
```solidity
File:         PirexRewards.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93-L99
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L151-L172
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L179-L197
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L432-L437
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L461-L465


```
```solidity
File:         vaults/AutoPxGlp.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94-L100
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106-L112
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118-L124
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130-L136

```
```solidity
File:         vaults/AutoPxGmx.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104-L110
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116-L122
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128-L134
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140-L146
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152-L158
```


### [GAS-02] <x> += <y> costs more gas than <x> = <x> + <y> for state variables. Same condition also valid in case of "-"

*Instances (6)*:
```solidity
File:         PirexRewards.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L361

```
```solidity
File:         PxERC20.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L119
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L124
```
```solidity
File:         vaults/PxGmxReward.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95
```


### [GAS-03] Arithmetic Operation should be unchecked when ever it does not cause overflow or underflow (Specially in case of loops) 

Should uncheck ++i can save extra gas

*Instances (3)*:
```solidity
File:         PirexRewards.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396

```


### [GAS-04] By making small logic change a significant amount of gas can be saved in case of loops
Here logic is that as >= costs less that > because The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

so during calculation of length of array we set memory variable to 1 less, so that in loop we use >= not > 

```
        uint256 len = rewardTokens.length - 1 ; 

        for (uint256 i; i <= len; ++i) {   
            if (address(rewardTokens[i]) == address(rewardToken)) {
                revert TokenAlreadyAdded();
            }
        }
```
*Instances (3)*:
```solidity
File:         PirexRewards.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L161-L167
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L348-L351
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L387-L396


```

### [GAS-05] Some Functions could be external instead of public
*Instances (6)*:

```solidity
File:         vaults/AutoPxGlp.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L151-L156
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L177-L181
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L449-L453
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L436-L440
```

```solidity
File:         vaults/AutoPxGmx.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L315-L319
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L339-L343
```

### [GAS-06] Gas can be save by adding a require condition for return value for distributeFees() function in PirexFees.sol contract 
In distributeFees() function, it first calculating the amount of ERC20 token, But it didn't check return value is non-zero or not
Without checking that it moving further, as solmate safeTransfer() allow zero amount transfer this function successfully completed with zero transfer.

if there is a require() that checks  ```uint distribution``` is non-zero can save further gas consumption 

*Instances (1)*:
```solidity
File:         PirexFees.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L116
```

