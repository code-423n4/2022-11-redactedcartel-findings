## QA Report - low risk

### Use `require` rather than `assert`
On failure, the `assert` function causes a `Panic` error and, unlike `require`, does not generate an error string. According to Solidity v0.8.17, "properly functioning code should never create a Panic."

[PirexGmx.sol: L225](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225)
```solidity
        assert(feeAmount + postFeeAmount == assets);
```
___
___


## QA Report non-critical

### Named `return` variable not used when a function returns
The named return variable `wethAmountIn` is never used

[IAutoPxGlp.sol: L5-19](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/interfaces/IAutoPxGlp.sol#L5-L19)
```solidity
    function compound(
        uint256 minUsdgAmount,
        uint256 minGlpAmount,
        bool optOutIncentive
    )
        external
        returns (
            uint256 wethAmountIn,
            uint256 pxGmxAmountOut,
            uint256 pxGlpAmountOut,
            uint256 totalPxGlpFee,
            uint256 totalPxGmxFee,
            uint256 pxGlpIncentive,
            uint256 pxGmxIncentive
        );
```
___
___


### TODOs and other open items
Comments that refer to open items should be addressed and modified or removed.

[PirexGmx.sol: L793-794](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L793-L794)
```solidity
            // This may not be necessary and is more of a hedge against a discrepancy between
            // the actual rewards and the calculated amounts. Needs further consideration
```
___
___


### Duplicate import 
___
[AutoPxGlp: L4-12](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L4-L12)
```solidity
import {PirexERC4626} from "src/vaults/PirexERC4626.sol";
import {PxGmxReward} from "src/vaults/PxGmxReward.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {PirexGmx} from "src/PirexGmx.sol";
import {PirexRewards} from "src/PirexRewards.sol";
```
Recommendation: Remove duplicate import `SafeTransferLib.sol`
___
___


### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields

[PirexFees.sol: L36-41](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L36-L41)
```solidity
    event DistributeFees(
        ERC20 indexed token,
        uint256 distribution,
        uint256 treasuryDistribution,
        uint256 contributorsDistribution
    );
```
Similarly, three `indexed` fields are required for the following `events`:

[PxGmxReward.sol: L22-27](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L22-L27)

[PxGmxReward.sol: L29-33](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L29-L33)

[AutoPxGmx.sol: L44-54](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L44-L54)

[AutoPxGlp.sol: L39-49](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L39-L49)

[PirexRewards.sol: L50-55](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L50-L55)

[PirexRewards.sol: L56-62](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L56-L62)

[PirexRewards.sol: L63-67](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L63-L67)

[PirexGmx.sol: L86-94](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L86-L94)

[PirexGmx.sol: L97-103](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L97-L103)

[PirexGmx.sol: L125-132](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L125-L132)

[PirexGmx.sol: L133-139](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L133-L139)

[PirexERC4626.sol: L27-32](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L27-L32)
___
___


### Missing `@param` statement for `constructor` 
Most of the `constructors` in `Redacted Cartel` include `@param` statements, where appropriate. For consistency, all `constructors` should incorporate them. The `constructor` below is missing `@params`:

[PirexERC4626.sol: L48-54](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L48-L54)
```solidity
    constructor(
        ERC20 _asset,
        string memory _name,
        string memory _symbol
    ) ERC20(_name, _symbol, _asset.decimals()) {
        asset = _asset;
    }
```
Missing: `@param  _asset`, `@param  _name` and `@param  _symbol`   
___
___


### `@return` is missing for some `functions` 
A couple of `functions` have complete NatSpec, with the exception of missing `@return` statements:

___
[PirexERC4626.sol: L233-241](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L233-L241)
```solidity
    /**
        @notice Override transfer method to allow for pre-transfer internal hook
        @param  to      address  Account receiving apxGLP
        @param  amount  uint256  Amount of apxGLP
    */
    function transfer(address to, uint256 amount)
        public
        override
        returns (bool)
```
___
[PirexERC4626.sol: L250-260](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L250-L260)
```solidity
    /**
        @notice Override transferFrom method to allow for pre-transfer internal hook
        @param  from    address  Account sending apxGLP
        @param  to      address  Account receiving apxGLP
        @param  amount  uint256  Amount of apxGLP
    */
    function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public override returns (bool) {
```
___
___

### NatSpec is wholly missing for some `functions` 
NatSpec is completely missing for some `public` or `external` `functions`. Note that NatSpec is required for `public` or `external` `functions` but not for `private` or `internal` ones.

___
[AutoPxGmx.sol: L315-319](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L315-L319)
```solidity
    function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public override returns (uint256 shares) {
```
NatSpec statements are similarly wholly or mostly missing (a couple include `@notice` statements) for the following  `public` or `external` `functions`:

[AutoPxGmx.sol: L339-343](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L339-L343)

[AutoPxGlp.sol: L433-440](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L433-L440)

[AutoPxGlp.sol: L446-453](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L446-L453)

[PirexRewards.sol: L85](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L85)

[PirexERC4626.sol: L60-63](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L60-L63)

[PirexERC4626.sol: L80-83](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L80-L83)

[PirexERC4626.sol: L99-103](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L99-L103)

[PirexERC4626.sol: L124-128](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L124-L128)

[PirexERC4626.sol: L154](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L154)

[PirexERC4626.sol: L156-160](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L156-L160)

[PirexERC4626.sol: L167-171](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L167-L171)

[PirexERC4626.sol: L178-182](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L178-L182)

[PirexERC4626.sol: L187](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L187)

[PirexERC4626.sol: L193-197](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L193-L197)

[PirexERC4626.sol: L204-208](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L204-L208)

[PirexERC4626.sol: L217-218](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L217-L218)

[PirexERC4626.sol: L221](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L221)

[PirexERC4626.sol: L225](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L225)

[PirexERC4626.sol: L229](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L229)

[IPirexRewards.sol: L7-13](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/interfaces/IPirexRewards.sol#L7-L13)

[IProducer.sol: L7-12](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/interfaces/IProducer.sol#L7-L12)

[IProducer.sol: L15-19](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/interfaces/IProducer.sol#L15-L19)

[IAutoPxGlp.sol: L5-19](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/interfaces/IAutoPxGlp.sol#L5-L19)
___
___

### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).
___
[PirexGmx.sol: L600](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L600)
```solidity
        if (!gmxVault.whitelistedTokens(token)) revert InvalidToken(token);
```
Suggestion: Change `whitelistedTokens` to `allowlistedTokens` 
___
Similarly, change `whitelist` to `allowlist` in the following instances of `whitelist` and its variants:

[AutoPxGlp.sol: L360-361](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L360-L361)

[AutoPxGlp.sol: L382](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L382)

[AutoPxGlp.sol: L389](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L389)

[PirexGmx.sol: L469](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L469)

[PirexGmx.sol: L574-575](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L474-L475)

[PirexGmx.sol: L607](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L607)

[PirexGmx.sol: L704](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L704)

[PirexGmx.sol: L728](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L728)
___
___
