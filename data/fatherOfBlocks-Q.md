**src/interfaces/IPirexRewards.sol**
- L4 - ERC20 is imported but it is never used, therefore it does not need to be there, it should be removed.


**src/interfaces/IProducer.sol**
- L4 - ERC20 is imported but it is never used, therefore it does not need to be there, it should be removed.


**src/vaults/AutoPxGmx.sol**
- L60 - The AlreadySet error is created, but it is never used, so it should be removed.

- L185/213 - The _totalSupply - shares operation is performed, but shares is an input to the function, therefore it is very possible to generate overflows, this should be validated in order to show a correct error so that it is easy for users to understand.


**src/vaults/AutoPxGlp.sol**
- The AutoPxGlp and AutoPxGmx contracts have a lot of shared code, therefore it would be very possible to modularize the code by creating an abstract contract or simply create two contracts that inherit from one that has everything shared implemented.
For example: constants, inheritance, variables in storages and default values, events, errors, setters and functions.

- L163/191 - The _totalSupply - shares operation is performed, but shares is an input to the function, therefore it is very possible to generate overflows, this should be validated in order to show a correct error so that it is easy for users to understand.

