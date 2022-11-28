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

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L117

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
<h3>[N-01] Event is missing indexed fields </h3>

Placing the “indexed” keyword in front of a parameter name will store it as a topic in the log record and make the field more quickly accessible to off-chain tools that parse events. Without the keyword “indexed”, it will be stored as data. Each event should use three indexed fields if there are three or more fields

Lines of code:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L34

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L35

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L36-L41

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L86-L94

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L95

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L96

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L97-L103

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L125-L132

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L133-L139

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L140

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L141

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L142

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L143

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L33

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L49

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L50-L55

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L56-L62

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L63-L67

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L35

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L36

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L37

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L38

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L39-L49

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L39

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L40

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L41

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L42

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L43

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L44-L54

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L27-L32

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L22-L27

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L28

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L29-L33

<h3>[N-02] Contract is importing the same Library twice </h3>

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L6

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L8

```solidity 
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol"; 
```
