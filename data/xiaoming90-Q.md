# Owner Can Become Owner Of The Implementation Contract

## Proof of Concept

If the implementation contract is not explicitly initialized during deployment, anyone can call the `initialize()` function of the `PirexRewards` implementation contract and become the owner of the implementation contract.

```solidity
File: PirexRewards.sol
85:     function initialize() public initializer {
86:         __Ownable_init();
87:     }
```

## Recommended Mitigation Steps

1. Consider using the constructor to initialize non-proxied contracts or run initialization function atomically along with contract construction each time.
2. Add access controls so only the authorized user is able to initialize it.
3. Make sure to call it immediately after deployment and verify the transaction succeeded.