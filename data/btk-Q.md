<h3>[L-01] Fee is not validated to be different than the existing one</h3>

While the fee is validated to be in ```FEE_MAX``` bounds, it is not validated to be different than the existing one

Lines of code: https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83-L94

Impact: Queueing the same fee will cause multiple abnormal events to be emitted, will ultimately result in a no-op situation.

Recommendation: Add an if condition to check that the new Fee is different than the current one.

```solidity
    function setFee(Fees f, uint256 fee) external onlyOwner {
        if (fee > FEE_MAX) revert InvalidFee();

        fees[f] = fee;

        emit SetFee(f, fee);
    }
 ```

<h3>[L-02] Missing 0 balance check before Distributing fees</h3>

Add an if condition to check that the balance is not 0. By adding these check, the function is able to revert before wasting a Gcoldsload in a function that may transfer 0 balance. 

Recommendation:

```solidity 
    function distributeFees(ERC20 token) external {
        if (token.balanceOf(address(this)) == 0) revert();
        uint256 distribution = token.balanceOf(address(this));
        uint256 treasuryDistribution = (distribution * treasuryFeePercent) /
            FEE_PERCENT_DENOMINATOR;
        uint256 contributorsDistribution = distribution - treasuryDistribution;

        emit DistributeFees(
            token,
            distribution,
            treasuryDistribution,
            contributorsDistribution
        );

        // Favoring push over pull to reduce accounting complexity for different tokens
        token.safeTransfer(treasury, treasuryDistribution);
        token.safeTransfer(contributors, contributorsDistribution);
    }
}
```

<h3>[N-01] Contract is importing the same Library twice </h3>

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L6

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L8

```solidity 
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol"; 
```
