GA1. https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L300
It is important to check the fee range to make sure it is no more than FEE_DONOMINATOR, otherwise line 222 will not make sense - one need to make sure it is no more than 100%. Also check `f' value to make sure is within `Fees`. 

GA2. https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L367
it is better to check for the last case ``GlpManager`` as well and treat other ``c`` value as an error. That is the purpose of enum. 
```
        if (c == Contracts.GlpManager) {
            gmxVault = IVault(contractAddress);
            return;
        }
 
       revert WrongContractParameter(c);
    
```

QA3: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L71-L72
Maybe change the two variables to ``rewardTrackerStakedGlp`` and ``rewardTrackerStakedGmx``. 

QA4: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L20
Use FEE_PERCENT_DENOMINATOR = 10000 might give a better precision





