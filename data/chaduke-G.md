G1. 
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L940
Cache state variable ``migratedTo`` since it is accessed twice in the function. 

G2. https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L54
https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L68

Cache ``block.timestamp``.

G3. https://code4rena.com/contests/2022-11-redacted-cartel-contest
Check whether it is necessary to change the state variables (instead of changing them nevertheless) can save gas:
```
       if(lastSupply != totalSupply){
            globalState.lastSupply = totalSupply.safeCastTo224();
       }

       if(blocktime != lastUpdate){
           globalState.lastUpdate = blocktime // blocktime is a cache of block.timestamp
           globalState.rewards = globalState.rewards +(blocktime - lastUpdate) *
                lastSupply;
       }
```

G4. https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L293-L295
Checking wether it is really necessary to change the state vairables can save gas.
```
       if(blocktime != lastUpdate){
                   u.lastUpdate = blocktime; // blocktime caches block.timestamp
                   u.rewards = u.rewards +  lastBlance *   (blocktime - lastUpdate);
       }

       if(lastBalance != balance){
                  u.lastBalance = balance.safeCastTo224();
       }
 ``

G5. https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L863
Change the argument ``string memory _delegationSpace`` to ``bytes32`` can save gas since no transformation is needed.

```
 function setDelegationSpace(
        bytes32 _delegationSpace,
        bool shouldClear
    ) external onlyOwner {
        if (shouldClear) {
            // Clear the delegation for the current delegation space
            clearVoteDelegate();
        }     
        delegationSpace = _delegationSpace

        emit SetDelegationSpace(_delegationSpace, shouldClear);
    }
```