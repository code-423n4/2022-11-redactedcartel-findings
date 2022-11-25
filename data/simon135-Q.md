##### make  these checks into a single  modifier to make the code more readable:
```
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
```
modifer nonzero(adddress t) {
if(t== address(0)) revert ZeroAddress();
}
```
##### rename  the `assets` var to `amount` for readability 
```
  function _computeAssetAmounts(Fees f, uint256 assets)
```
make into `uint256 amount`
:219L: PirexGmx.sol
##### rename the `postFeeAmount` and `feeAmount`
it's very confusing because  the `postFeeAmount` is the real fee amount and `feeAmount`  is just the fee  subtracted
instead:
`feeAmount`= the fee
`postFeeAmount` the `amount-fee`
#### add parameters to `beforeDeposit` to stop huge slippage loss and to make it more useable 
```
        if (totalAssets() != 0) beforeDeposit(address(0), 0, 0);

```
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L383