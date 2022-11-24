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


QA5: PirexRewards.sol and PirexRewards.sol share four functions: ``globalAccure()``, ``userAccruee()``, ``harvet()``, and ``claim``, refactoring into a library or 
tinheriting from a base class will be helpful

QA6: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L953
the documentation should be ``called by the owner at the new contract". 

QA7: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L234
The ``_calculateRewards`()`` function differentiate four cases of reward types by the two boolean variables ``isBasedReward`` and ``useGmx``. It might better to introduce the following enum type
```
     enum RewardTypes {gmxBasedRewards, glpBasedRewards, gmxEsGmxRewards, glpEsGmxRewards}
     function _calculateRewards(RewardTypes rt){
     }
```

QA8: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxGmx.sol#L19
The ``PirexGmx`` contract has a ``burn()`` function that has an empty body, which overides the behavior of that of the base contract ``PxERC20``. As a result, PxGmx cannot be burned. It this is the intention of the design, then it is better to document it and use a revert statement in the burn implemetation to make it explicit. 

QA9: https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L292
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L921
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L940
The allowance set at line L292 for ``gmx`` to ``stakedGmx`` needs to be reset to zero when migrating to a new contract.



 

