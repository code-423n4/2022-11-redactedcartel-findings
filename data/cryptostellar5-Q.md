### 01 `safeIncreaseAllowance` and `safeDecreaseAllowance` should be used instead of `safeApprove`

*Number of Instances Identified: 9*

-   `safeApprove()` has been deprecated in favour of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`
-   using `approve()` might fail because some tokens (eg. USDT) don’t work when changing the allowance from an existing non-zero allowance value

### Recommended Mitigation Steps

Update instances of `approve()` and `safeApprove()` to `safeIncreaseAllowance()`.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol

```
292: gmx.safeApprove(address(stakedGmx), type(uint256).max);
348: gmx.safeApprove(address(stakedGmx), 0);
353: gmx.safeApprove(contractAddress, type(uint256).max);
507: t.safeApprove(glpManager, tokenAmount);
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol

```
87: gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
347: stakedGlp.safeApprove(platform, amount);
391: erc20Token.safeApprove(platform, tokenAmount);
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol

```
96: gmxBaseReward.safeApprove(address(SWAP_ROUTER), type(uint256).max
97: gmx.safeApprove(_platform, type(uint256).max);
```


### 02 EVENT IS MISSING INDEXED FIELDS

*Number of Instances Identified: 34*

Each `event` should use three `indexed` fields if there are three or more fields


https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol

```
35: event WithdrawalPenaltyUpdated(uint256 penalty);
36: event PlatformFeeUpdated(uint256 fee);
37: event CompoundIncentiveUpdated(uint256 incentive);
38: event PlatformUpdated(address _platform);
39: event Compounded(
```


https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol

```
86: event ConfigureGmxState(
95: event SetFee(Fees indexed f, uint256 fee);
96: event SetContract(Contracts indexed c, address contractAddress);
97: event DepositGmx(
125: event ClaimRewards(
133: event ClaimUserReward(
140: event InitiateMigration(address newContract)
141: event CompleteMigration(address oldContract);
142: event SetDelegationSpace(string delegationSpace, bool shouldClear);
143: event SetVoteDelegate(address voteDelegate);
```


https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol

```
34: event SetFeeRecipient(FeeRecipient f, address recipient);
35: event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
36: event DistributeFees(
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
33: event SetProducer(address producer);
49: event RemoveRewardToken(ERC20 indexed producerToken, uint256 removalIndex);
50: event GlobalAccrue(
56: event UserAccrue(
63: event Harvest(
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol

```
39: event PoolFeeUpdated(uint24 _poolFee);
40: event WithdrawalPenaltyUpdated(uint256 penalty);
41: event PlatformFeeUpdated(uint256 fee);
42: event CompoundIncentiveUpdated(uint256 incentive);
43: event PlatformUpdated(address _platform);
44: event Compounded(
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol

```
27: event Deposit(
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol

```
21: event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);
22: event UserAccrue(
28: event Harvest(uint256 rewardAmount);
29: event PxGmxClaimed(
```


### 03 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

*Number of Instances Identified: 1*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol

```
207: function getUserState(ERC20 producerToken, address user)
```


### 04 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol

```
225: assert(feeAmount + postFeeAmount == assets);
```
