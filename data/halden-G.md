## [G-01] Using unchecked blocks to save gas - Increments in for loop can be unchecked
PirexRewards.sol: [163](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163), [351](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351), [396](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396)

## [G-02] Superfluous event fields
block.timestamp is added to event information by default so adding them manually wastes gas

PirexRewards.sol: [323-328](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L323-L328)
```            
emit GlobalAccrue(
                producerToken,
                block.timestamp,
                totalSupply,
                rewards
                );
```

[297](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L297)
`emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);`

## [G-03] Superfluous event fields for msg.sender
Information about msg.sender can be getted from the transaction, so adding additional field in the event wastes gas

PirexRewards.sol: [121](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L121), [143](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L143)

PirexGmx.sol: [281](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L281), [409](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L409), [453](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L453), [533](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L533), [665](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L665)

AutoPxGlp.sol: [285](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L285), [319](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L319)

AutoPxGmx.sol: [302](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L302), [334](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L334), [359](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L359), [402](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L402)


PirexERC4626.sol: [75](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L75), [94](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L94), [117](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L117), [143](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L143)


PirexERC4626.sol: [130](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L130)

## [G-04] Add unchecked {} where the operands can not underflow/overflow because of a previous check
Penalty will never be smaller than assets.
AutoPxGlp.sol: [168](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L168)

AutoPxGmx.sol: [190](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L190)
