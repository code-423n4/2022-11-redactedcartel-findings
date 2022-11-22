## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data.

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient);
35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
```
## Complementary Codehash Checks
Consider adding an optional codehash check for each contract zero address check since the latter cannot guarantee a matching address has been inputted.

For instance, the following code block may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L50-L56

```
    constructor(address _treasury, address _contributors, bytes32 _treasuryCodeHash, bytes32 _contributorsCodeHash) Owned(msg.sender) {
        if (_treasury == address(0) || _treasury.codehash != _treasuryCodeHash) revert ZeroAddress();
        if (_contributors == address(0) || _contributors.codehash != _contributorsCodeHash) revert ZeroAddress();

        treasury = _treasury;
        contributors = _contributors;
    }
```
All other instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
67:        if (recipient == address(0)) revert ZeroAddress();
```
[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
30:        if (_pirexRewards == address(0)) revert ZeroAddress();
```
[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
41:        if (_pxGmx == address(0)) revert ZeroAddress();
```
[File:AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
82:        if (_gmxBaseReward == address(0)) revert ZeroAddress();
83:        if (_gmx == address(0)) revert ZeroAddress();
84:        if (_asset == address(0)) revert ZeroAddress();

87:        if (_platform == address(0)) revert ZeroAddress();
88:        if (_rewardsModule == address(0)) revert ZeroAddress();

153:        if (_platform == address(0)) revert ZeroAddress();
```
## Complementary Threshold Checks
Consider adding an optional threshold check for each zero amount check to avoid any input of edge value.

For instance, the following code line may be refactored as follows, catering for `6 <= _decimals <= 18`:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L33

```
        if (_decimals == 0 || _decimals < 6 || _decimals > 18) revert ZeroAmount(); 
```
All other instances entailed:

[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
105:        if (_poolFee == 0) revert ZeroAmount();

257:        if (fee == 0) revert InvalidParam();
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient); // @ f

63:    function setFeeRecipient(FeeRecipient f, address recipient) // @ f

69:        emit SetFeeRecipient(f, recipient); // @ f

71:        if (f == FeeRecipient.Treasury) { // @ f
```
[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
71:        UserState storage u = userRewardStates[user]; // @ u
```
## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value.

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
67:        emit SetFeeRecipient(f, recipient);

93:        emit SetTreasuryFeePercent(_treasuryFeePercent);
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
109:        emit PoolFeeUpdated(_poolFee);

121:        emit WithdrawalPenaltyUpdated(penalty);

133:        emit PlatformFeeUpdated(fee);

145:        emit CompoundIncentiveUpdated(incentive);

157:        emit PlatformUpdated(_platform);
```
## Use `delete` to Clear Variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic. 

For instance, the `a = 0` instance below should be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L118

```
            delete userRewardStates[msg.sender].rewards = 0;
```
## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are the instances entailed:

[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
54:            (block.timestamp - globalState.lastUpdate) *

57:        globalState.lastUpdate = block.timestamp.safeCastTo32();

61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);

77:            (block.timestamp - u.lastUpdate);

79:        u.lastUpdate = block.timestamp.safeCastTo32();

83:        emit UserAccrue(user, block.timestamp, balance, rewards);
```
## Unnecessary Emission of `block.timestamp`
Any event emitted by Solidity includes the timestamp and block number implicitly. Adding it the second time just increases the cost of the transaction.

Here are the instances entailed:

[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);

83:        emit UserAccrue(user, block.timestamp, balance, rewards);
```
## Assignment of Boolean in Conditional Check
Making a variable assignment in a conditional statement deviates from the standard use and intention of the check and can easily lead to confusion. 

Here is the instance entailed:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L355

```
        require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```
Consider moving the needed assignment before the conditional statement by having the code lines rewritten as follows.

```
        assets = previewRedeem(shares)) != 0;
        require(assets, "ZERO_ASSETS");
```
All other instances entailed:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L394-L398

```
        if (
            (shares = supply == 0
                ? assets
                : assets.mulDivDown(supply, totalAssets() - assets)) == 0
        ) revert ZeroShares();
```
 