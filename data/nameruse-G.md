### Gas Optimizations

||Issue|Instance|
|---|---|---|
|[GAS-01]|Optimize Func Name to Save Gas| At least 2|
|[GAS-02]|Unchecked For Loop can save more gas than pre-increment|1|
|[GAS-03]|Marking Func as payable can save gas per call|21|

### Details   
#### [GAS-01] Optimize Func Name to Save Gas

Sorting commonly used public (external) functions to appear first in the bytecode can save gas over time.   
22 addition gas for each sorted rank below the first. 

instances:2

File: PirexRewards.sol
Line: 373
[Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

Renaming it so function signature has leading zero will do that which in this case is: claim_Um7(erc20,address. (Selector: 0x0000e3e4)

```javascript
function claim(ERC20 producerToken, address user) external
```

File: PirexGMX.sol
Line: 739
[Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

Renaming it so function signature has leading zero will do that which in this case is: claimRewards_6H10(). (Selector: 0x0000c10a)

```javascript
    function claimRewards()
        external
        onlyPirexRewards
        returns (
            ERC20[] memory producerTokens,
            ERC20[] memory rewardTokens,
            uint256[] memory rewardAmounts
        )
```

#### [GAS-02] Unchecked For Loop can save more gas than pre-increment

Since with current setup it seems producer is going to be PirexGMX contract, plen will alway be 4 so could be converted to unchecked for loop to further save gas.

instances:1

File: PirexRewards.sol
Line: 348-351
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```javascript
 // Calculate the amount of rewards accrued by the user up to this call
        uint256 pLen = _producerTokens.length;

        // Iterate over the producer tokens and update reward state
        for (uint256 i; i < pLen; ++i) 
```

#### [GAS-03] Marking Func as payable can save gas per call

Can be marked payable to save around 21 gas per call, as OP codes related to retreiving msg.value won't be called. Since it is restricted from normal users, they won't be able to mistakanley send ether

instances:21

File: AutoPxGlp.sol
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```javascript
95    function setWithdrawalPenalty(uint256 penalty) external onlyOwner

108    function setPlatformFee(uint256 fee) external onlyOwner

121    function setCompoundIncentive(uint256 incentive) external onlyOwner

134   function setPlatform(address _platform) external onlyOwner
```

File: AutoPxGmx.sol
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```javascript
105    function setPoolFee(uint24 _poolFee) external onlyOwner

118    function setWithdrawalPenalty(uint256 penalty) external onlyOwner

131    function setPlatformFee(uint256 fee) external onlyOwner

144    function setCompoundIncentive(uint256 incentive) external onlyOwner

157    function setPlatform(address _platform) external onlyOwner
```

File: PirexGMX.sol
[Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```javascript
273    function configureGmxState() external onlyOwner whenPaused

315    function setContract(Contracts c, address contractAddress)

869    function setDelegationSpace(string memory _delegationSpace, bool shouldClear) external onlyOwner 

891    function setVoteDelegate(address voteDelegate) external onlyOwner

903    function clearVoteDelegate() public onlyOwner

918    function setPauseState(bool state) external onlyOwner

931    function initiateMigration(address newContract) external whenPaused onlyOwner
```

File: PirexRewards.sol
[Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```javascript
152    function addRewardToken(ERC20 producerToken, ERC20 rewardToken) external onlyOwner

181    function removeRewardToken(ERC20 producerToken, uint256 removalIndex) external onlyOwner
```

File: PirexFees.sol
[Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```javascript
64    function setFeeRecipient(FeeRecipient f, address recipient) external onlyOwner

85    function setTreasuryFeePercent(uint8 _treasuryFeePercent) external onlyOwner        
```