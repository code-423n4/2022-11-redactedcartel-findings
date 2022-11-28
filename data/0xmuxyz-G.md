### Report title
- Using `++i` for incrementing `i` inside of loop should be replaced with `unchecked { ++ }`


### Code Snippet that has not been optimized yet
- Below are lines that has not been optimized yet:
  - https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163-L167
  - https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351-L363
  - https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396-L415


### Description
- The `unchecked` block is able to use from solidity version 0.8.0
- The variable `i` inside of loop is not able to overflow because of the condition `i < length`, where length is defined as uint256. The maximum value `i` that can reach is `max(uint)-1` . Therefore, incrementing `i` inside `unchecked` block is safe. Also consuming gas of incrementing `i` inside `unchecked` block is less than using `++i`.


### Recommendation
- Incrementing `i` inside `unchecked` block should be used.
   - eg). In case of the first unoptimized-lines (https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163-L167), incrementing `i` inside `unchecked` block could be used like below:
```solidity
        for (uint256 i; i < len;) {
            if (address(rewardTokens[i]) == address(rewardToken)) {
                revert TokenAlreadyAdded();
            }

            // Incrementing i inside unchecked block
            unchecked {
                i++
            }
        }
```