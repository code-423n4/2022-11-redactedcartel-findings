 ### a += b costs more gas than a = a + b for state variables

Using the addition operator instead of plus-equals saves Gas

There are 4 instances of this issue:

```

File:  src/PirexRewards.sol

631:   producerState.rewardStates[rewardTokens[i]] += r;


File: src/PxERC20.sol

  90:     balanceOf[to] += amount;
124:     balanceOf[to] += amount;


File: src/vaults/PxGmxReward.sol

95:  rewardState += rewardAmount;


```