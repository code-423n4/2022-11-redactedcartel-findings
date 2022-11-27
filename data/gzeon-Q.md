## Low

### Invalid maxDeposit

Per EIP-4626, `maxDeposit` returns the maximum amount of underlying assets that can be deposited in a single deposit call by the receiver. When `balance > 0`, you can only deposit `type(uint256).max - balance` amount of token.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L217-L219

```solidity
    function maxDeposit(address) public view virtual returns (uint256) {
        return type(uint256).max;
    }
```

### Invalid maxMint

Per EIP-4626, `maxMint` returns the maximum amount of underlying assets that can be deposited in a single deposit call by the receiver. When `totalsupply > 0`, you can only deposit `type(uint256).max - totalsupply` amount of token.

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L221-L223

```solidity
    function maxMint(address) public view virtual returns (uint256) {
        return type(uint256).max;
    }
```

## Non-Critical

### Unused constant

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L22-L23

```solidity
    uint256 public constant EXPANDED_DECIMALS = 1e30;

```

### Inconsistant FEE_PERCENT_DENOMINATOR

Multiple value of `FEE_PERCENT_DENOMINATOR` is used across the codebase, this might easily lead to configuration mistake.

### ReentrancyGuard is not initialized

https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L66-L88

```solidity
    constructor(
        address _gmxBaseReward,
        address _asset,
        address _pxGmx,
        string memory _name,
        string memory _symbol,
        address _platform,
        address _rewardsModule
    ) PxGmxReward(_pxGmx) PirexERC4626(ERC20(_asset), _name, _symbol) {
        if (_gmxBaseReward == address(0)) revert ZeroAddress();
        if (_asset == address(0)) revert ZeroAddress();
        if (bytes(_name).length == 0) revert InvalidAssetParam();
        if (bytes(_symbol).length == 0) revert InvalidAssetParam();
        if (_platform == address(0)) revert ZeroAddress();
        if (_rewardsModule == address(0)) revert ZeroAddress();

        gmxBaseReward = ERC20(_gmxBaseReward);
        platform = _platform;
        rewardsModule = _rewardsModule;

        // Approve the Uniswap V3 router to manage our base reward (inbound swap token)
        gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
    }
```