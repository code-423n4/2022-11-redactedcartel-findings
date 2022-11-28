### CHECK OF SAFETRANSFER SUCCED
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L114 and #L115
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L71  and L90
safetransfer implementation returns a bool value if transfer has succeded or not. It's suggested to check with a if/revert/error check


### CHECK SANITY OF ROUTING ADDRESS
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L274 to L278
This function is setting state variables by calling variables of an external contract. 
Emit allows to check if values are valid, but it's better to check if returned values are valid and avoid that owner unpause the contract with broken addresses

### USE OF INDEXED KEYWORD IN EMIT
It is suggested to use in Emit of important actions (like Deposit or Withdraw, or setting functions) the indexed keyword
For instance in the followin example (src/PirexGmx.sol#L532) indexed can be used for msg.sender, receiver and token addresses for indexing.

    src/PirexGmx.sol#L532
        emit DepositGlp( 
            msg.sender,
            receiver,
            token,
            tokenAmount,
            minUsdg,
            minGlp,
            deposited,
            postFeeAmount,
            feeAmount
        );

### REVERT WITH ERROR IN CASE OF UNDERFLOW/OVERFLOW IN PxERC20
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L119 L67 AND L85
I suggest to add an if/revert/error check if amount <= balanceOf, otherwise the EVM reverts for underflow and could be hard to understand the reason why. There are some roundings in vault 4626 that could lead to underflow. 
