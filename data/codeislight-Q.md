### Following are QA improvements and suggestions to improve on UX:
- compiling all error messages and struct within a single interface sol file and move it to interfaces folder:
 
        interface ICommon{
            error ZeroAmount();
            error InvalidAssetParam();
            error ExceedsMax();
            error InvalidParam();
            error ZeroShares();
            error ZeroAddress();
            error InvalidFeePercent();
            error AlreadySet();
            error insufficientAllowance();
            error NotContract();
            error InvalidToken(address token);
            error NotPirexRewards();
            error InvalidFee();
            error EmptyString();
            error NotMigratedTo();
            error PendingMigration();
            error TokenAlreadyAdded();
            error EmptyArray();
            // using 112 bits should be sufficient, since 2**112-1 ~= 5.19e33
            // since GMX totalSuply: 8.87e24
            // since GLP totalSuply: 4.42e+26
            // since shares issuance is not aggressive
            // therefore 112 bits is safe to be used
            struct GlobalState {
                uint32 lastUpdate;
                uint112 lastSupply;
                uint112 rewards;
            }
            
            struct UserState {
                uint32 lastUpdate;
                uint112 lastBalance;
                uint112 rewards;
            }
        }

- Arithemetic errors need to be replaced with revert errors for a good UX and debugging:
 in PirexRewards.removeRewardToken(), it can be rewritten to validate the array length:

        function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
            external
            onlyOwner
        {
            if (address(producerToken).code.length == 0) revert NotContract();
    
            ERC20[] storage rewardTokens = producerTokens[producerToken]
                .rewardTokens;
            uint256 len = rewardTokens.length; // added change
            if(len == 0) revert EmptyArray(); // added change
            uint256 lastIndex = rewardTokens.length - 1;
    
            if (removalIndex != lastIndex) {
                // Set the element at removalIndex to the last element
                rewardTokens[removalIndex] = rewardTokens[lastIndex];
            }
    
            rewardTokens.pop();
    
            emit RemoveRewardToken(producerToken, removalIndex);
        }

- prevent allowance underflow arithmetic error in AutoPxGmx:

        function withdraw(
            uint256 assets,
            address receiver,
            address owner
        ) public override returns (uint256 shares) {
            // Compound rewards and ensure they are properly accounted for prior to withdrawal calculation
            compound(poolFee, 1, 0, true);
    
            shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
    
            if (msg.sender != owner) {
                uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
    
                if(allowed < shares) revert insufficientAllowance(); // added change
    
                if (allowed != type(uint256).max)
                    allowance[owner][msg.sender] = allowed - shares;
            }
    
            _burn(owner, shares);
    
            emit Withdraw(msg.sender, receiver, owner, assets, shares);
    
            asset.safeTransfer(receiver, assets);
        }
    
        function redeem(
            uint256 shares,
            address receiver,
            address owner
        ) public override returns (uint256 assets) {
            // Compound rewards and ensure they are properly accounted for prior to redemption calculation
            compound(poolFee, 1, 0, true);
    
            if (msg.sender != owner) {
                uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
    
                if(allowed < shares) revert insufficientAllowance(); // added change
    
                if (allowed != type(uint256).max)
                    allowance[owner][msg.sender] = allowed - shares;
            }
    
            // Check for rounding error since we round down in previewRedeem.
            require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
    
            _burn(owner, shares);
    
            emit Withdraw(msg.sender, receiver, owner, assets, shares);
    
            asset.safeTransfer(receiver, assets);
        }

- prevent allowance underflow arithmetic error in PirexERC4626:

        function withdraw(
            uint256 assets,
            address receiver,
            address owner
        ) public virtual returns (uint256 shares) {
            shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
    
            if (msg.sender != owner) {
                uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
    
                if(allowed < shares) revert insufficientAllowance();
    
                if (allowed != type(uint256).max)
                    allowance[owner][msg.sender] = allowed - shares;
            }
    
            beforeWithdraw(owner, assets, shares);
    
            _burn(owner, shares);
    
            emit Withdraw(msg.sender, receiver, owner, assets, shares);
    
            asset.safeTransfer(receiver, assets);
    
            afterWithdraw(owner, assets, shares);
        }
    
        function redeem(
            uint256 shares,
            address receiver,
            address owner
        ) public virtual returns (uint256 assets) {
            if (msg.sender != owner) {
                uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
                
                if(allowed < shares) revert insufficientAllowance();
    
                if (allowed != type(uint256).max)
                    allowance[owner][msg.sender] = allowed - shares;
            }
    
            // Check for rounding error since we round down in previewRedeem.
            require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
    
            beforeWithdraw(owner, assets, shares);
    
            _burn(owner, shares);
    
            emit Withdraw(msg.sender, receiver, owner, assets, shares);
    
            asset.safeTransfer(receiver, assets);
    
            afterWithdraw(owner, assets, shares);
        }

- prevent any possibility of underflow arithmetic revert in PxGmxReward.claim(), by checking that globalRewards >= userRewards

        function claim(address receiver) external {
            if (receiver == address(0)) revert ZeroAddress();
    
            IAutoPxGlp(address(this)).compound(1, 1, true);
            _userAccrue(msg.sender);
    
            uint256 globalRewards = globalState.rewards;
            uint256 userRewards = userRewardStates[msg.sender].rewards;
    
            // Claim should be skipped and not reverted on zero global/user reward
            if (globalRewards >= userRewards && userRewards != 0) {  // added change
                // Update global and user reward states to reflect the claim
                globalState.rewards = (globalRewards - userRewards).safeCastTo112();
                delete userRewardStates[msg.sender].rewards;
    
                // Transfer the proportionate reward token amounts to the recipient
                uint256 _rewardState = rewardState;
                uint256 amount = (_rewardState * userRewards) / globalRewards;
    
                if (amount != 0) {
                    // Update reward state (i.e. amount) to reflect reward tokens transferred out
                    rewardState = _rewardState - amount;
    
                    pxGmx.safeTransfer(receiver, amount);
    
                    emit PxGmxClaimed(msg.sender, receiver, amount);
                }
            }
        }

 - prevernt any possibility of underflow revert in PxERC20.transferFrom():

        function transferFrom(
            address from,
            address to,
            uint256 amount
        ) public override returns (bool) {
            uint256 allowed = allowance[from][msg.sender]; // Saves gas for limited approvals.
    
            if(allowed < amount) revert insufficientAllowance(); // added change
    
            if (allowed != type(uint256).max)
                allowance[from][msg.sender] = allowed - amount;
    
            balanceOf[from] -= amount;
    
            // Cannot overflow because the sum of all user
            // balances can't exceed the max uint256 value.
            unchecked {
                balanceOf[to] += amount;
            }
    
            emit Transfer(from, to, amount);
    
            pirexRewards.userAccrue(this, from);
            pirexRewards.userAccrue(this, to);
    
            return true;
        }
- owner controlled functions need to check on contract code size validation, instance: PirexRewards.setProducer() need to check that _producer is an actual contract and not just a zero address. note that checking that _producer is a contract is sufficient and wouldn't require any need to check ZeroAddress validation.

        function setProducer(address _producer) external onlyOwner {
            if (_producer.code.length == 0) revert NotContract(); // added change
    
            producer = IProducer(_producer);
    
            emit SetProducer(_producer);
        }

            function addRewardToken(ERC20 producerToken, ERC20 rewardToken)
                external
                onlyOwner
            {
                if (address(producerToken).code.length == 0) revert NotContract();// added change
                if (address(rewardToken).code.length == 0) revert NotContract();// added change
        
                // Check if the token has been added previously for the specified producer
                ProducerToken storage p = producerTokens[producerToken];
                ERC20[] memory rewardTokens = p.rewardTokens;
                uint256 len = rewardTokens.length;
        
                for (uint256 i; i < len; ++i) {
                    if (address(rewardTokens[i]) == address(rewardToken)) {
                        revert TokenAlreadyAdded();
                    }
                }
        
                p.rewardTokens.push(rewardToken);
        
                emit AddRewardToken(producerToken, rewardToken);
            }
        
            function removeRewardToken(ERC20 producerToken, uint256 removalIndex)
                external
                onlyOwner
            {
                if (address(producerToken).code.length == 0) revert NotContract();// added change
        
                ERC20[] storage rewardTokens = producerTokens[producerToken]
                    .rewardTokens;
                uint256 len = rewardTokens.length;
                if(len == 0) revert EmptyArray();
                uint256 lastIndex = rewardTokens.length - 1;
        
                if (removalIndex != lastIndex) {
                    // Set the element at removalIndex to the last element
                    rewardTokens[removalIndex] = rewardTokens[lastIndex];
                }
        
                rewardTokens.pop();
        
                emit RemoveRewardToken(producerToken, removalIndex);
            }

        function setRewardRecipientPrivileged(
            address lpContract,
            ERC20 producerToken,
            ERC20 rewardToken,
            address recipient
        ) external onlyOwner {
            if (lpContract.code.length == 0) revert NotContract();
            if (address(producerToken).code.length == 0) revert NotContract();// added change
            if (address(rewardToken).code.length == 0) revert NotContract();// added change
            if (recipient == address(0)) revert ZeroAddress();

            producerTokens[producerToken].rewardRecipients[lpContract][
                rewardToken
            ] = recipient;

            emit SetRewardRecipientPrivileged(
                lpContract,
                producerToken,
                rewardToken,
                recipient
            );
        }

        function unsetRewardRecipientPrivileged(
            address lpContract,
            ERC20 producerToken,
            ERC20 rewardToken
        ) external onlyOwner {
            if (lpContract.code.length == 0) revert NotContract();
            if (address(producerToken).code.length == 0) revert NotContract();// added change
            if (address(rewardToken).code.length == 0) revert NotContract();// added change

            delete producerTokens[producerToken].rewardRecipients[lpContract][
                rewardToken
            ];

            emit UnsetRewardRecipientPrivileged(
                lpContract,
                producerToken,
                rewardToken
            );
        }
- checking contract size length instead of zeroAddress in PirexGmx.setContract:

        function setContract(Contracts c, address contractAddress)
            external
            onlyOwner
        {
            if (contractAddress.code.length == 0) revert NotContract(); // added change

            emit SetContract(c, contractAddress);
            // ...
        }

- enhance PirexGmx.setPauseState() flow to avoid resetting to already assigned value:

        function setPauseState(bool state) external onlyOwner {
            if (state) {
                _requireNotPaused();
                _pause();
            } else {
                _requirePaused();
                _unpause();
            }
        }