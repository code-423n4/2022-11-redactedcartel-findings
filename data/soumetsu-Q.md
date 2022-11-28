[01] Remove check in line 492, the same check is made in the execution of gmxRewardRouterV2.mintAndStakeGlp

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L492

```
    function mintAndStakeGlp(address _token, uint256 _amount, uint256 _minUsdg, uint256 _minGlp) external nonReentrant returns (uint256) {
        require(_amount > 0, "RewardRouter: invalid _amount");
    
        ...

    }
```

[02] slight alignment error with return statement

assert(postFeeAmount + feeAmount == assets) may be better to match the order of the return variables

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225

[03] add additional check to ensure fee is not 0. assuming the fee is never intended to be 0

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300-L306