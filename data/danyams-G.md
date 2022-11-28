# Increments in For Loops can go Unchecked

There are 3 different for loops in PirexRewards.sol in harvest( ), claim( ), and addRewardToken( ).  In each of these instances, the for loop will stop if the uint256, i, is equal to a uint256 that is previously assigned to the length of an array.  Since i is being compared to another uint256, there is no way for it to overflow.  

For example, I would change the for loop in harvest() to the following:

...
for (uint256 i; i < pLen;) {
            ERC20 p = _producerTokens[i];
            uint256 r = rewardAmounts[i];

            // Update global reward accrual state and associate with the update of reward state
            ProducerToken storage producerState = producerTokens[p];

            _globalAccrue(producerState.globalState, p);

            if (r != 0) {
                producerState.rewardStates[rewardTokens[i]] += r;
            }

            unchecked { i ++; }
        }
...

