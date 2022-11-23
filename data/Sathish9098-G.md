## [G1] Unchecked Arithmetic operations . 

        Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

<https://docs.soliditylang.org/en/v0.8.0/control-structures.html#checked-or-unchecked-arithmetic>

>  There are 13 instances of this issue.

>  FILE:  2022-11-redactedcartel/src/PirexGmx.sol

       256:  uint256 cumulativeRewardPerToken = r.cumulativeRewardPerToken() +
              ((blockReward * precision) / r.totalSupply());

<https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L795-L798> 

     805:     rewardAmounts[3] = esGmxRewards - rewardAmounts[2];


 >  FILE:  2022-11-redactedcartel/src/PxERC20.sol

     117:     allowance[from][msg.sender] = allowed - amount;


> FILE:  FILE :   2022-11-redactedcartel/src/PirexFees.sol

     104:      uint256 contributorsDistribution = distribution - treasuryDistribution;

> FILE:   2022-11-redactedcartel/src/PirexRewards.sol

     315:  uint256 rewards = globalState.rewards +
                (block.timestamp - lastUpdate) *
                lastSupply;

     390:   p.globalState.rewards = globalRewards - userRewards;

     407:   p.rewardStates[rewardToken] = rewardState - amount;

> FILE:  2022-11-redactedcartel/src/vaults/AutoPxGlp.sol

     168:    return assets - penalty;

     264:    pxGmxAmountOut = pxGmx.balanceOf(address(this)) - preClaimPxGmxAmount;

> FILE:   2022-11-redactedcartel/src/vaults/PirexERC4626.sol

      110:    allowance[owner][msg.sender] = allowed - shares;

> FILE:  2022-11-redactedcartel/src/vaults/PxGmxReward.sol

     117:   globalState.rewards = globalRewards - userRewards;

    126:     rewardState = _rewardState - amount;

## 

## [G2] Use uint8 Can Increase Gas Cost. Can use uint256 instead of uint8 . Uint256 uses less gas than uint8 in loops and other situations.

> A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations. 

>  There are 2 instances of this issue.

> FILE:  2022-11-redactedcartel/src/PxERC20.sol

       28:   uint8 _decimals;

>  FILE :   2022-11-redactedcartel/src/PirexFees.sol

      83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)

##

## [G3]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES. SAME  <X> -= <Y> COSTS MORE GAS THAN <X> = <X> - <Y> FOR STATE VARIABLES

>  There are 6 instances of this issue.

 >  FILE:  2022-11-redactedcartel/src/PxERC20.sol

      85:    balanceOf[msg.sender] -= amount;

      90:    balanceOf[to] += amount;

      119:  balanceOf[from] -= amount;

      124:  balanceOf[to] += amount;

> FILE:   2022-11-redactedcartel/src/PirexRewards.sol

      361:   producerState.rewardStates[rewardTokens[i]] += r;

>FILE:    2022-11-redactedcartel/src/vaults/PxGmxReward.sol

      95: rewardState += rewardAmount;

##

## [G4]  INCREMENTS CAN BE UNCHECKED

  > In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

>  There are 3 instances of this issue.

 > FILE:  2022-11-redactedcartel/src/PirexRewards.sol

       163:   for (uint256 i; i < len; ++i) {

> FILE:   2022-11-redactedcartel/src/PirexRewards.sol

       351:    for (uint256 i; i < pLen; ++i) {

      396:    for (uint256 i; i < rLen; ++i) {

##

##  [G5] WHEN EVER WE USE || WE DON'T WANT TO CHECK ALL CONDITIONS. ANY ONE CONDITION true THEN THE OVER ALL CONDITION CHECKS BECOME true EVEN OTHER CONDITIONS ARE false. SO ITS POSSIBLE SAVE MORE GAS FEE TO SKIP CONDITION CHECKS 

>  There are 2 instances of this issue.

> FILE:   2022-11-redactedcartel/src/PirexRewards.sol

    314:   if (block.timestamp != lastUpdate || totalSupply != lastSupply) { 

> FILE:  2022-11-redactedcartel/src/vaults/AutoPxGlp.sol

    163:  uint256 penalty = (_totalSupply == 0 || _totalSupply - shares == 0)

##

## [G6]  SPLITTING if STATEMENTS THAT USE && SAVES GAS

>  There are 2 instances of this issue.

> FILE:   2022-11-redactedcartel/src/PirexRewards.sol

      385:  if (globalRewards != 0 && userRewards != 0) {

      115:    2022-11-redactedcartel/src/vaults/PxGmxReward.sol

     115: if (globalRewards != 0 && userRewards != 0) {


##

##   [G7]  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

>  If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

>  There are 9 instances of this issue.

> FILE:  2022-11-redactedcartel/src/vaults/AutoPxGlp.sol

        94:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

       106:  function setPlatformFee(uint256 fee) external onlyOwner {

       118:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

       130:   function setPlatform(address _platform) external onlyOwner {

> FILE:   2022-11-redactedcartel/src/vaults/AutoPxGmx.sol

       104:   function setPoolFee(uint24 _poolFee) external onlyOwner {

       116:   function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

       128:   function setPlatformFee(uint256 fee) external onlyOwner {

       140:   function setCompoundIncentive(uint256 incentive) external onlyOwner {

        152:   function setPlatform(address _platform) external onlyOwner {

   



   



    

 





        