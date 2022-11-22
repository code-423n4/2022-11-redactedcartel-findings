### Impact
The `PirexRewards` contract  allows for user to set the recipient for reward token via the `setRewardRecipient ()` function as seen here https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L107-L126. In setting the recipient, the user is allowed to set the `producerToken` and `rewardToken`.  The contract owner is allowed to add reward token as seen here https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L151. As such, it would be pointless for a user to input `producerToken` and `rewardToken` which has not been added by the contract owner. 

### Proof of Concept

This can be proved via the test case which can be added to the `PirexRewards.t.sol` contract 
```
function testSetRewardRecipientSSSS() external {
        ERC20 producerToken = pxGlp;
        ERC20 rewardToken = weth;
        ERC20[] memory rewardTokensBeforePush = pirexRewards.getRewardTokens(
            producerToken
        );

        assertEq(0, rewardTokensBeforePush.length);
        address recipient = address(this);
        address oldRecipient = pirexRewards.getRewardRecipient(
            address(this),
            producerToken,
            rewardToken
        );

        assertEq(address(0), oldRecipient);
        assertTrue(recipient != oldRecipient);

        vm.expectEmit(true, true, true, true, address(pirexRewards));

        emit SetRewardRecipient(
            address(this),
            producerToken,
            rewardToken,
            recipient
        );

        pirexRewards.setRewardRecipient(producerToken, rewardToken, recipient);

        assertEq(
            recipient,
            pirexRewards.getRewardRecipient(
                address(this),
                producerToken,
                rewardToken
            )
        );
        ERC20[] memory rewardTokensAfterPush = pirexRewards.getRewardTokens(
            producerToken
        );
        assertEq(0, rewardTokensAfterPush.length);
        // assertEq(address(rewardToken), address(rewardTokensAfterPush[0]));
    }
```

The function does not check to ensure that only allowed `producerTokens` and `rewardTokens` is set by the user. 