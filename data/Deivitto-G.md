# GAS

## Increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks


https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L163
        for (uint256 i; i < len; ++i) {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L351
        for (uint256 i; i < pLen; ++i) {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L396
            for (uint256 i; i < rLen; ++i) {


### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header



## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks


https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L20
    uint8 public constant FEE_PERCENT_DENOMINATOR = 100;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L23
    uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L28
    uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L20
    uint8 public constant FEE_PERCENT_DENOMINATOR = 100;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L23
    uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L28
    uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;



### Mitigation
Consider using some data type that uses 32 bytes, for example uint256


## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;

Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks


https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/PxGmxReward.sol#L19
    mapping(address => UserState) public userRewardStates;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L22
        mapping(address => UserState) userStates;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L24
        mapping(address => mapping(ERC20 => address)) rewardRecipients;
- - - - -

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L23
        mapping(ERC20 => uint256) rewardStates;

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L31
    mapping(ERC20 => ProducerToken) public producerTokens;
- - - - -


### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.



## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage. 

Values are saved into state variables and then are red again, they can be stored in local variables and then assigned / read for emit

### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L272-L289


### Mitigation
Save the values to local variables and set directly the value to avoid unnecessary storage read to save some gas


## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks


https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L65
        onlyOwner

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L85
        onlyOwner

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L104
    function setPoolFee(uint24 _poolFee) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L116
    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L128
    function setPlatformFee(uint256 fee) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L140
    function setCompoundIncentive(uint256 incentive) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L152
    function setPlatform(address _platform) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L94
    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L106
    function setPlatformFee(uint256 fee) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L118
    function setCompoundIncentive(uint256 incentive) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L130
    function setPlatform(address _platform) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L93
    function setProducer(address _producer) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L153
        onlyOwner

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L181
        onlyOwner

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L437
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L465
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L272
    function configureGmxState() external onlyOwner whenPaused {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L300
    function setFee(Fees f, uint256 fee) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L315
        onlyOwner

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L865
    ) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L884
    function setVoteDelegate(address voteDelegate) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L895
    function clearVoteDelegate() public onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L909
    function setPauseState(bool state) external onlyOwner {

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L924
        onlyOwner

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L959
        onlyOwner

### Mitigation
Consider adding payable to save gas
