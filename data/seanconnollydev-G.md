In `PirexRewards.sol` - `harvest()`: Consider using an unchecked increment in the for loop for gas efficiency.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351

Current:
```
    function harvest()
        public
        returns (
            ERC20[] memory _producerTokens,
            ERC20[] memory rewardTokens,
            uint256[] memory rewardAmounts
        )
    {
        (_producerTokens, rewardTokens, rewardAmounts) = producer
            .claimRewards();
        uint256 pLen = _producerTokens.length;

        // Iterate over the producer tokens and update reward state
        for (uint256 i; i < pLen; ++i) {
            ERC20 p = _producerTokens[i];
            uint256 r = rewardAmounts[i];

            // Update global reward accrual state and associate with the update of reward state
            ProducerToken storage producerState = producerTokens[p];

            _globalAccrue(producerState.globalState, p);

            if (r != 0) {
                producerState.rewardStates[rewardTokens[i]] += r;
            }
        }

        emit Harvest(_producerTokens, rewardTokens, rewardAmounts);
    }
```

Modified:
```
    function harvest()
        public
        returns (
            ERC20[] memory _producerTokens,
            ERC20[] memory rewardTokens,
            uint256[] memory rewardAmounts
        )
    {
        (_producerTokens, rewardTokens, rewardAmounts) = producer
            .claimRewards();
        uint256 pLen = _producerTokens.length;

        // Iterate over the producer tokens and update reward state
        for (uint256 i; i < pLen; ) {
            ERC20 p = _producerTokens[i];
            uint256 r = rewardAmounts[i];

            // Update global reward accrual state and associate with the update of reward state
            ProducerToken storage producerState = producerTokens[p];

            _globalAccrue(producerState.globalState, p);

            if (r != 0) {
                producerState.rewardStates[rewardTokens[i]] += r;
            }

            unchecked {
                ++i;
            }
        }

        emit Harvest(_producerTokens, rewardTokens, rewardAmounts);
    }
```