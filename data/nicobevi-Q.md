# QA


## Extends contracts using the correspinding interfaces
A good practice when you have an interface representing a contract is to extend such contract with the corresponding interface. In that way you can be sure that the interface definition is accurate.

### Where
1. `./vaults/AutoPxGlp.sol` should use `./interfaces/IAutoPxGlp.sol`
2. `./PirexRewards.sol` should use `./interfaces/IPirexRewards.sol`

## Unnecessary assert validation

### Where
https://github.com/code-423n4/2022-11-redactedcartel/blob/74af1b6efe4c0c3f4796e0899a6c37fc745abdd7/src/PirexGmx.sol/#L217-L226

```solidity
  function _computeAssetAmounts(Fees f, uint256 assets)
      internal
      view
      returns (uint256 postFeeAmount, uint256 feeAmount)
  {
      feeAmount = (assets * fees[f]) / FEE_DENOMINATOR;
      postFeeAmount = assets - feeAmount;

      assert(feeAmount + postFeeAmount == assets);
  }
```

The assert validation is unnecesary since it will be always true (there is no rounding error risk there).

### Solution
Remove the assert line to save gas

```solidity
  function _computeAssetAmounts(Fees f, uint256 assets)
      internal
      view
      returns (uint256 postFeeAmount, uint256 feeAmount)
  {
      feeAmount = (assets * fees[f]) / FEE_DENOMINATOR;
      postFeeAmount = assets - feeAmount;
  }
```