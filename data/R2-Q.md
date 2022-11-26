# 1. ``PirexGmx``: rewards may be claimed even if protocol is paused

### Impact
Even if you will find some bug/vulnerability and pause the protocol, anyone can claim rewards. Because there are not ``whenNotPaused`` modifiers in ``claimUserReward()`` and ``claimRewards()`` functions

### Recommended Mitigation Steps
Add modifier ``whenNotPaused``


# 2. ``PirexRewards.addRewardToken()``: any token may be added

### Impact
There are not checks in the ``PirexRewards.addRewardToken()`` function, so owner may add any token. 
But if it's not ``WETH/WAVAX`` or ``GMX`` - it will be ignored in ``PirexRewards.claimUserReward()``:
```
        if (token == address(pxGmx)) {
            // Mint pxGMX for the user - the analog for esGMX rewards
            pxGmx.mint(receiver, postFeeAmount);

            if (feeAmount != 0) pxGmx.mint(address(pirexFees), feeAmount);
        } else if (token == address(gmxBaseReward)) {
            // @audit-ok тут может не хватать баланса???
            // Как он пополняется, что это за токен вообще нахер
            gmxBaseReward.safeTransfer(receiver, postFeeAmount);

            if (feeAmount != 0)
                gmxBaseReward.safeTransfer(address(pirexFees), feeAmount);
        }
```
So if token doesn't match any clause, it will be ignored

It may lead to misunderstanding for users

### Recommended Mitigation Steps
Do not allow to add any token except of ``WETH/WAVAX`` and ``GMX``