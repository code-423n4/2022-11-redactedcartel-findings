##### make  these checks into a single  modifier to make the code more readable:
```js
        if (_pxGmx == address(0)) revert ZeroAddress();
        if (_pxGlp == address(0)) revert ZeroAddress();
        if (_pirexFees == address(0)) revert ZeroAddress();
        if (_pirexRewards == address(0)) revert ZeroAddress();
        if (_delegateRegistry == address(0)) revert ZeroAddress();
        if (_gmxBaseReward == address(0)) revert ZeroAddress();
        if (_gmx == address(0)) revert ZeroAddress();
        if (_esGmx == address(0)) revert ZeroAddress();
        if (_gmxRewardRouterV2 == address(0)) revert ZeroAddress();
        if (_stakedGlp == address(0)) revert ZeroAddress();
```
instead, make it into a modifier 
```js
modifer nonzero(adddress t) {
if(t== address(0)) revert ZeroAddress();
}
```
##### rename  the `assets` var to `amount` for readability 
```js
  function _computeAssetAmounts(Fees f, uint256 assets)
```
make into `uint256 amount`
:219L: PirexGmx.sol
##### rename the `postFeeAmount` and `feeAmount`
it's very confusing because  the `postFeeAmount` is the real fee amount and `feeAmount`  is just the fee  subtracted
instead:
`feeAmount`= the fee
`postFeeAmount` the `amount-fee`
#####  add parameters to `beforeDeposit` to stop huge slippage loss and to make it more useable 
```js
        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);

```
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L383

#####   if `distrbutorbalance= 0`  then rewards won't be processed even though there are rewards that need to get processed

```js

     uint256 blockReward = pendingRewards > distributorBalance
            ? distributorBalance
            : pendingRewards;
        uint256 precision = r.PRECISION();
        uint256 cumulativeRewardPerToken = r.cumulativeRewardPerToken() +
            ((blockReward * precision) / r.totalSupply());
```
so if `pendingRewards > distributorBalance`  The amount is `distributorBalance` instead of  the actual pending amounts
ex:

`pendingRewards= 1ether` 
`distributorBalance=0`
The rewards will  be `0` and no rewards will be distributed   because  of the ternary operator wrong use
instead, make  the calculation into

```js
blockReward=pendingRewards;
        uint256 cumulativeRewardPerToken = r.cumulativeRewardPerToken() +
            ((blockReward * precision) / r.totalSupply());

```
#### small amount of tokens won't be distributed
```js
    function distributeFees(ERC20 token) external {
        uint256 distribution = token.balanceOf(address(this));
        uint256 treasuryDistribution = (distribution * treasuryFeePercent) /
            FEE_PERCENT_DENOMINATOR;
        uint256 contributorsDistribution = distribution - treasuryDistribution;
		
```
if `distribution` is less than `FEE_PERCENT_DENOMINATOR` then it will be zero and be a waste of gas by  the caller
add a zero check to make sure it's not zero.
#### make sure the total supply can be less than uint256 because for some tokens this can cause an  issue
```js
   globalState.lastSupply = totalSupply.safeCastTo224();
```
#### the function will revert if `totalPxGlpFee` is less than the `Incentive` which is possible with the calculations
```js
    uint256 newAssets = totalAssets() - preClaimTotalAssets;
        if (newAssets != 0) {
            totalPxGlpFee = (newAssets * platformFee) / FEE_DENOMINATOR;
            pxGlpIncentive = optOutIncentive
                ? 0
                : (totalPxGlpFee * compoundIncentive) / FEE_DENOMINATOR;

            if (pxGlpIncentive != 0)
                asset.safeTransfer(msg.sender, pxGlpIncentive);
            asset.safeTransfer(owner, totalPxGlpFee - pxGlpIncentive);
        }

```
##### approve all should be used it can cause risk of hacks and loss of funds, on the front end approve how much users need 
```
        gmxBaseReward.safeApprove(address(SWAP_ROUTER), type(uint256).max);
        gmx.safeApprove(_platform, type(uint256).max);
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L98-L99
#####  if time passes  between states then more tokens will cause a more significant balance just make sure it's updated by a right way and that rewards are not updated too much 
```js
        uint256 rewards = u.rewards +
            u.lastBalance *
            (block.timestamp - u.lastUpdate);
        u.lastUpdate = block.timestamp.safeCastTo32();
        u.lastBalance = balance.safeCastTo224();
        u.rewards = rewards;

```
#### certain times these calculations can revert causing issues  
ex:
`rewardState= 1e18`
`userRewards=100`
`globalRewards=10`
```
(1e18 *100) / 10= 1e19
```
it can really not work when the tokens are going through a loop and the amounts get bigger causing a revert at the end of the loop and making the function revert and rewards won't work.
```js
        uint256 rewardState = p.rewardStates[rewardToken];
                uint256 amount = (rewardState * userRewards) / globalRewards;

                if (amount != 0) {
                    // Update reward state (i.e. amount) to reflect reward tokens transferred out
                    p.rewardStates[rewardToken] = rewardState - amount;
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L418
make sure the calculation is good and make a limit for how many `rewardTokens` you can loop through
##### centralization risk, the minter and burner can mint any number of tokens and burn any number of tokens
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PxERC20.sol#L46
