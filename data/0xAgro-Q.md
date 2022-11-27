# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Copied Solmate Code Missing NatSpec Function Headers|21|
|[NC-02]|Events Not Indexed|20|
|[NC-03]|Underscore Notation Not Used|10|
|[NC-04]|Order of Functions Not Compliant With Solidity Docs|5
|[NC-05]|No Documentation For Interfaces|4|
|[NC-06]|Power of Ten Literal > `10e3` Not In Scientific Notation|1|
|[NC-07]|Magic Number Used|1|

### [NC-01] Copied Solmate Code Missing NatSpec Function Headers

Although the modifications of the solmate [ERC4626.sol](https://github.com/transmissions11/solmate/blob/main/src/mixins/ERC4626.sol) contract are noted at the top of [PirexERC4626.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol) there are no NatSpec comments per function. Function documentation helps readability and understanding as a developer reads the code top to bottom.

#### Findings:

*/src/vaults/PirexERC4626.sol*

All [PirexERC4626.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol) functions excluding `transfer` and `transferFrom` lack NatSpec headers.

### [NC-02] Events Not Indexed

It is best practice to have 3 indexed fields per event. Indexed fields help off-chain tools analyze on-chain activity through filtering. 

#### Findings: 

*/src/vaults/AutoPxGmx.sol*
Links: [39](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L39), [40](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L40), [41](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L41), [42](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L42), [43](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L43).
```solidity
39:	event PoolFeeUpdated(uint24 _poolFee);
40:	event WithdrawalPenaltyUpdated(uint256 penalty);
41:	event PlatformFeeUpdated(uint256 fee);
42:	event CompoundIncentiveUpdated(uint256 incentive);
43:	event PlatformUpdated(address _platform);
```

*/src/PirexFees.sol*
Links: [34](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L34), [35](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L35).
```solidity
34:	event SetFeeRecipient(FeeRecipient f, address recipient);
35:	event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
```

*/src/vaults/PxGmxReward.sol*
Links: [21](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21), [28](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L28).
```solidity
21:	event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);
28:	event Harvest(uint256 rewardAmount);
```

*/src/vaults/AutoPxGlp.sol*
Links: [35](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L35), [36](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L36), [37](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L37), [38](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L38).
```solidity
35:	event WithdrawalPenaltyUpdated(uint256 penalty);
36:	event PlatformFeeUpdated(uint256 fee);
37:	event CompoundIncentiveUpdated(uint256 incentive);
38:	event PlatformUpdated(address _platform);
```

*/src/PirexGmx.sol*
Links: [125](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L125), [141](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L141), [142](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L142), [143](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L143), [144](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L144).
```solidity
125:	event ClaimRewards(
141:	event InitiateMigration(address newContract);
142:	event CompleteMigration(address oldContract);
143:	event SetDelegationSpace(string delegationSpace, bool shouldClear);
144:	event SetVoteDelegate(address voteDelegate);
```

*/src/PirexRewards.sol*
Links: [33](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L33), [63](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L63).
```solidity
33:	event SetProducer(address producer);
63:	event Harvest(
```

### [NC-03] Underscore Notation Not Used / Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

#### Findings:

*/src/vaults/AutoPxGlp.sol*
Links: [19](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L19), [21](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L21), [25](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L25), [26](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L26).
```solidity
19:	uint256 public constant MAX_PLATFORM_FEE = 2000;
21:	uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
25:	uint256 public platformFee = 1000;
26:	uint256 public compoundIncentive = 1000;
```

*/src/vaults/AutoPxGmx.sol*
Links: [21](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L21), [22](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L22), [23](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L23), [26](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L26), [29](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L29), [30](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L30).
```solidity
21:	uint256 public constant MAX_PLATFORM_FEE = 2000;
22:	uint256 public constant FEE_DENOMINATOR = 10000;
23:	uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
26:	uint24 public poolFee = 3000;
29:	uint256 public platformFee = 1000;
30:	uint256 public compoundIncentive = 1000;
```

### [NC-04] Order of Functions Not Compliant With Solidity Docs

The [Solidity documentation suggests](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) the following function ordering:  constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

The following contracts are not compliant: 
[PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol): Internal functions are positioned before external functions.
[AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol): External functions are positioned after public functions. 
[AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol): External functions are positioned after public functions. 
[PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol): Public functions are positioned after internal functions.
[PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol): External functions are positioned after internal functions.

### [NC-05] No Documentation For Interfaces

It is good practice to have documentation for interfaces, even if there is documentation for the functions that inherit the interface.

#### Findings: 
[IPirexRewards.col](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IPirexRewards.sol), [IProducer.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IProducer.sol), [IAutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/interfaces/IAutoPxGlp.sol), [Common.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/Common.sol).

### [NC-06] Power of Ten Literal > `10e3` Not In Scientific Notation

Power of ten literals > `10e3` are easier to read when expressed in scientific notation. Consider expressing large powers of ten in scientific notation (Ex. 10e5).

#### Findings:

*/src/vaults/AutoPxGlp.sol*
Links: [20](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L20).
```solidity
20:	uint256 public constant FEE_DENOMINATOR = 10000;
```

### [NC-07] Magic Number Used

The `uint` `18` is used without indication of it's use. For code clarity it is best to not use magic numbers and replace them with well-named constants.

#### Findings:

*/src/PirexFees.sol*
Links: [11](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L11).
```solidity
11:	PxERC20(_pirexRewards, "Pirex GMX", "pxGMX", 18)
```