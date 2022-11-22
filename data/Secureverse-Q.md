### [LOW-01] distributeFees() function in PirexFees.sol contract didn't have condition to check for value

In distributeFees() function, it first calculating the amount of ERC20 token, But it didn't check return value is non-zero or not
Without checking that it moving further, as solmate safeTransfer() allow zero amount transfer this function successfully completed with zero token transfer.

*Instances (1)*:
```solidity
File:         PirexFees.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L116
```

### [LOW-02] Two different compiler version used

solidity version 0.8.17 used by below

```solidity
File:            Common.sol
File:            PirexFees.sol
File:            PirexGmx.sol
File:            PirexRewards.sol
File:            PxERC20.sol
File:            PxGmx.sol

File:            vaults/AutoPxGlp.sol
File:            vaults/AutoPxGmx.sol
File:            vaults/PxGmxReward.sol
```

solidity version >=0.8.0 used by below

```solidity
File:            vaults/PirexERC4626.sol
```

### [LOW-03] Return value of ERC20 transfer() and transferFrom() is not checked
return value from transfer() and transferFrom() are catched but not checked

There should be a require() statement for checking that value

*Instances (2)*:

```solidity
File:            vaults/PirexERC4626.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L243
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L261
```


### [NC-01] Instead of long number of zeros number try to use scientific notetations

*Instances (2)*:

```solidity
File:         PirexGmx.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L47
```

### [NC-02] Should be a named return value

*Instances (4)*:

```solidity
File:         PirexRewards.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L234
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L247
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L263
```
```solidity
File:        vaults/AutoPxGlp.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L191-L194
```

### [NC-03] Blank function

function doing nothing whenever call occurs to it.
Should be removed or at implement something in it, may be an event emission on call

*Instances (1)*:

```solidity
File:         PxGmx.sol

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L19-L24
```