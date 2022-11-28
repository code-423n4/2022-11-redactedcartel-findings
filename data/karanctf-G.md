
> * Declare vairable outside of loop in functions as it gets relocated and consume more gas
> * Remove `block.timestamp` and `block.number` as they are added to `event` information by default so adding them manually wastes gas.
```
src/vaults/PxGmxReward.sol:61:        emit GlobalAccrue(block.timestamp, totalSupply, rewards);
src/vaults/PxGmxReward.sol:83:        emit UserAccrue(user, block.timestamp, balance, rewards);
src/PirexRewards.sol:297:        emit UserAccrue(producerToken, user, block.timestamp, balance, rewards);
```
> * Use immutable rather than constant for keccak256() calls
```
src/PxERC20.sol:9:    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
src/PxERC20.sol:10:    bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```
> * Declare `p` and `r` outside for loop
```
src/PirexRewards.sol-351-        for (uint256 i; i < pLen; ++i) {
src/PirexRewards.sol:352:            ERC20 p = _producerTokens[i];
src/PirexRewards.sol-353-            uint256 r = rewardAmounts[i];
```
> * use unchecked in ++i in for loops