# QA report

## Author: rotcivegaf

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Duplicate import | 1 |
| [N&#x2011;02] | Unused error | 1 |
| [N&#x2011;03] | Event is missing `indexed` fields | 31 |
| [N&#x2011;04] | The function `beforeDeposit` should call in all cases | 6 |
| [N&#x2011;05] | Hard code contract address | 1 |
| [N&#x2011;06] | Unused library | 1 |

## Non-critical

### [N‑01] Duplicate import

```solidity
File: src/vaults/AutoPxGlp.sol

/// @audit: The SafeTransferLib was imported in L6
8:import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
```

### [N‑02] Unused error

```solidity
File: src/vaults/AutoPxGmx.sol

60:    error AlreadySet();
```

### [N‑03] Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```solidity
File: src/PirexFees.sol

34:    event SetFeeRecipient(FeeRecipient f, address recipient);

35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);

36:    event DistributeFees(
37:        ERC20 indexed token,
38:        uint256 distribution,
39:        uint256 treasuryDistribution,
40:        uint256 contributorsDistribution
41:    );
```

```solidity
File: src/PirexGmx.sol

 86:    event ConfigureGmxState(
 87:        address indexed caller,
 88:        RewardTracker rewardTrackerGmx,
 89:        RewardTracker rewardTrackerGlp,
 90:        RewardTracker feeStakedGlp,
 91:        RewardTracker stakedGmx,
 92:        address glpManager,
 93:        IVault gmxVault
 94:    );

 95:    event SetFee(Fees indexed f, uint256 fee);

 96:    event SetContract(Contracts indexed c, address contractAddress);

 97:    event DepositGmx(
 98:        address indexed caller,
 99:        address indexed receiver,
100:        uint256 deposited,
101:        uint256 postFeeAmount,
102:        uint256 feeAmount
103:    );

125:    event ClaimRewards(
126:        uint256 baseRewards,
127:        uint256 esGmxRewards,
128:        uint256 gmxBaseRewards,
129:        uint256 glpBaseRewards,
130:        uint256 gmxEsGmxRewards,
131:        uint256 glpEsGmxRewards
132:    );

133:    event ClaimUserReward(
134:        address indexed receiver,
135:        address indexed token,
136:        uint256 amount,
137:        uint256 rewardAmount,
138:        uint256 feeAmount
139:    );

140:    event InitiateMigration(address newContract);

141:    event CompleteMigration(address oldContract);

142:    event SetDelegationSpace(string delegationSpace, bool shouldClear);

143:    event SetVoteDelegate(address voteDelegate);
```

```solidity
File: src/PirexRewards.sol

33:    event SetProducer(address producer);

49:    event RemoveRewardToken(ERC20 indexed producerToken, uint256 removalIndex);

50:    event GlobalAccrue(
51:        ERC20 indexed producerToken,
52:        uint256 lastUpdate,
53:        uint256 lastSupply,
54:        uint256 rewards
55:    );

56:    event UserAccrue(
57:        ERC20 indexed producerToken,
58:        address indexed user,
59:        uint256 lastUpdate,
60:        uint256 lastBalance,
61:        uint256 rewards
62:    );

63:    event Harvest(
64:        ERC20[] producerTokens,
65:        ERC20[] rewardTokens,
66:        uint256[] rewardAmounts
67:    );
```

```solidity
File: src/vaults/AutoPxGlp.sol

35:    event WithdrawalPenaltyUpdated(uint256 penalty);

36:    event PlatformFeeUpdated(uint256 fee);

37:    event CompoundIncentiveUpdated(uint256 incentive);

38:    event PlatformUpdated(address _platform);

39:    event Compounded(
40:        address indexed caller,
41:        uint256 minGlp,
42:        uint256 gmxBaseRewardAmount,
43:        uint256 pxGmxAmountOut,
44:        uint256 pxGlpAmountOut,
45:        uint256 totalPxGlpFee,
46:        uint256 totalPxGmxFee,
47:        uint256 pxGlpIncentive,
48:        uint256 pxGmxIncentive
49:    );
```

```solidity
File: src/vaults/AutoPxGmx.sol

39:    event PoolFeeUpdated(uint24 _poolFee);

40:    event WithdrawalPenaltyUpdated(uint256 penalty);

41:    event PlatformFeeUpdated(uint256 fee);

42:    event CompoundIncentiveUpdated(uint256 incentive);

43:    event PlatformUpdated(address _platform);

44:    event Compounded(
45:        address indexed caller,
46:        uint24 fee,
47:        uint256 amountOutMinimum,
48:        uint160 sqrtPriceLimitX96,
49:        uint256 gmxBaseRewardAmountIn,
50:        uint256 gmxAmountOut,
51:        uint256 pxGmxMintAmount,
52:        uint256 totalFee,
53:        uint256 incentive
54:    );
```

```solidity
File: src/vaults/PirexERC4626.sol

27:    event Deposit(
28:        address indexed caller,
29:        address indexed owner,
30:        uint256 assets,
31:        uint256 shares
32:    );
```

```solidity
File: src/vaults/PxGmxReward.sol

21:    event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);

22:    event UserAccrue(
23:        address indexed user,
24:        uint256 lastUpdate,
25:        uint256 lastSupply,
26:        uint256 rewards
27:    );

28:    event Harvest(uint256 rewardAmount);

29:    event PxGmxClaimed(
30:        address indexed account,
31:        address receiver,
32:        uint256 amount
33:    );
```

### [N‑04] The function `beforeDeposit` should call in all cases

Remove the `if (totalAssets() != 0)`, the function `beforeDeposit` should call in all cases and the case of `totalAssets` it's 0 should be resolve inside the function `beforeDeposit`

```solidity
File: src/vaults/AutoPxGlp.sol

338:        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);

380:        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);

423:        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
```

```solidity
File: src/vaults/AutoPxGmx.sol

379:        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);
```

```solidity
File: src/vaults/PirexERC4626.sol

65:        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);

85:        if (totalAssets() != 0) beforeDeposit(receiver, assets, shares);
```

### [N‑05] Hard code contract address

Consider assign in the constructor

```solidity
File: src/vaults/PirexERC4626.sol

18:    IV3SwapRouter public constant SWAP_ROUTER =
19:        IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45);
```

### [N-06] Unused library

```solidity
File: src/PirexRewards.sol

 5:import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";

16:    using SafeTransferLib for ERC20;
```