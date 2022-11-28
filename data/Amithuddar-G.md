1)++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops

File:2022-11-redactedcartel\src\PirexRewards.sol
  163,9:         for (uint256 i; i < len; ++i) {
  351,9:         for (uint256 i; i < pLen; ++i) {
  396,13:             for (uint256 i; i < rLen; ++i) {

2)X = X + Y OR X = X - Y CAN BE USED INSTEAD OF X += Y OR X -= Y

x = x + y or x = x - y costs less gas than x += y or x -= y. For example, v += 27 can be changed to v = v + 27 in the following code.   
  
  
File:2022-11-redactedcartel\src\PirexRewards.sol
  361,61:                 producerState.rewardStates[rewardTokens[i]] += r; 

File:2022-11-redactedcartel\src\PxERC20.sol
  90,27:             balanceOf[to] += amount;
  124,27:             balanceOf[to] += amount; 

File:2022-11-redactedcartel\src\vaults\PxGmxReward.sol
  95,25:             rewardState += rewardAmount;

File:2022-11-redactedcartel\src\PxERC20.sol
  85,31:         balanceOf[msg.sender] -= amount;
  119,25:         balanceOf[from] -= amount; 
  
3)Multiple address mappings can be combined into a single mapping of an address to a struct

Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

File:2022-11-redactedcartel\src\PirexRewards.sol
  22,9:         mapping(address => UserState) userStates;
  23,9:         mapping(ERC20 => uint256) rewardStates;
  24,9:         mapping(address => mapping(ERC20 => address)) rewardRecipients;
   31,5:     mapping(ERC20 => ProducerToken) public producerTokens;  

File:2022-11-redactedcartel\src\external\RewardTracker.sol
  783,5:     mapping(address => bool) public isDepositToken;
  784,5:     mapping(address => mapping(address => uint256))
  789,5:     mapping(address => uint256) public balances;
  790,5:     mapping(address => mapping(address => uint256)) public allowances;
  793,5:     mapping(address => uint256) public override stakedAmounts;
  794,5:     mapping(address => uint256) public claimableReward;
  795,5:     mapping(address => uint256) public previousCumulatedRewardPerToken;
  796,5:     mapping(address => uint256) public override cumulativeRewards;
  797,5:     mapping(address => uint256) public override averageStakedAmounts;
  802,5:     mapping(address => bool) public isHandler;   