### Gas optimizations suggestions:
- simplify expressions that are constant, so it's not being unnecessarily calculated on each call:
FEE_DENOMINATOR - withdrawalPenalty = 9700

- use of unchecked when underflow/overflow is unlikely, for example in AutoPxGlp.compound() the following snippet can be wrapped with unchecked:

        unchecked {
            uint256 newAssets = totalAssets() - preClaimTotalAssets;
            if (newAssets != 0) {
                totalPxGlpFee = (newAssets * platformFee) / FEE_DENOMINATOR;
                pxGlpIncentive = optOutIncentive
                    ? 0
                    : (totalPxGlpFee * compoundIncentive) / FEE_DENOMINATO        
                if (pxGlpIncentive != 0)
                    asset.safeTransfer(msg.sender, pxGlpIncentive);
                // impossible to underflow due to the factor * compoundIncentive / FEE_DENOMINATOR
                asset.safeTransfer(owner, totalPxGlpFee - pxGlpIncentive);
        
                // Track the amount of pxGMX received
                // impossible to underflow, since pxGmxAmountOut >= 0
                pxGmxAmountOut = pxGmx.balanceOf(address(this)) - preClaimPxGmxAmount;
            }
        }

- remove safeCaseTo32 for block.timestamp, since it would overflow and lead to a revert anyway after (2**32-1 = 4294967295 = 83 years left) and therefore cast it right away to uint32(block.timestamp).

- refactoring structs variable to use uint112 instead of uint224:
   -> since 2**112-1 ~= 5.19e33
   -> since GMX totalSuply: 8.87e24
   -> since GLP totalSuply: 4.42e+26
   -> shares issuance is not aggressive
   ---> therefore: 112 bits is safe to be used

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

the following library may be used to cast to uint112

        library SafeCast112{
            error OVERFLOW();
            function safeCastTo112(uint256 x) internal pure returns (uint112 y) {
                if(x >= 1 << 112) revert OVERFLOW();
                y = uint112(x);
            }
        }

test cases:

        import "forge-std/Test.sol";
        import {SafeCast112} from "src/SafeCast112.sol";
        
        contract SafeCast112Test is Test {
            function testSafeCastTo112() public {
                
                assertEq(SafeCast112.safeCastTo112(2.5e27), 2.5e27);
                assertEq(SafeCast112.safeCastTo112(2.5e18), 2.5e18);
            }
        
            function testFailSafeCastTo112() public pure {
                SafeCast112.safeCastTo112(type(uint112).max + 1);
            }
        
            function testSafeCastTo112(uint256 x) public {
                x = bound(x, 0, type(uint112).max);
        
                assertEq(SafeCast112.safeCastTo112(x), x);
            }
        
            function testFailSafeCastTo128(uint256 x) public {
                x = bound(x, type(uint112).max + 1, type(uint256).max);
        
                SafeCast112.safeCastTo112(x);
            }
        }

- using delete storage of resetting value to 0, instance in PxGmxReward.claim():

        userRewardStates[msg.sender].rewards = 0; // gas waster
        delete userRewardStates[msg.sender].rewards; // gas saver

- within autoPxGlp and autoPxGmx, owner fees are better be updated in a state variable, and then withdrawn using a special onlyOwner function that withdraws the owner fees, instead of costing the user on each deposit/redeem/withdraw.
- removing SafeTransfer and safeTransferFrom, it was made for irregular tokens that might not return boolean nor revert on transfer/transferFrom, but since GLP, GMX tokens are adhering to ERC20 standard and there is no need to check the return calldata whether it reverts or not, since it eithers hit an error statement and revert the tx or return true as a sign of success.

- moving event emitting to the end of the transaction, in the case of transaction revert, it deosn't include its cost, instance in PirexERC4626.redeem()

            _burn(owner, shares);
    
            emit Withdraw(msg.sender, receiver, owner, assets, shares);
    
            asset.safeTransfer(receiver, assets);
    
            afterWithdraw(owner, assets, shares);

turned into :

            _burn(owner, shares);
    
            asset.safeTransfer(receiver, assets);
    
            afterWithdraw(owner, assets, shares);
    
            emit Withdraw(msg.sender, receiver, owner, assets, shares);