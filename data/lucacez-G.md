### USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: AutoPxGmx.sol [line 355](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L355)
```
        require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

File: PirexERC4626.sol [line 68](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L68)
```
        require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
```
File: PirexERC4626.sol [line 137](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PirexERC4626.sol#L137)
```
        require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

### X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

File: PirexRewards.sol [line 361](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L361)
```
                producerState.rewardStates[rewardTokens[i]] += r;
```

File: PxERC20.sol [line 90](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L90)
```
            balanceOf[to] += amount;
```

File: PxERC20.sol [line 124](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L124)
```
            balanceOf[to] += amount;
```
File: PxGmxReward.sol [line 95](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/PxGmxReward.sol#L95)
```
            rewardState += rewardAmount;
```
### USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

File: PirexFees.sol [line 20](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L20)
```
    uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
```
File: PirexFees.sol [line 23](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexFees.sol#L23)
```
    uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
```
File: PirexGmx.sol [line 44](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L44)
```
    uint256 public constant FEE_DENOMINATOR = 1_000_000;
```
File: PirexGmx.sol [line 47](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L47)
```
    uint256 public constant FEE_MAX = 200_000;
```
File: PxERC20.sol [line 9](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L9)
```
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
```
File: PxERC20.sol [line 10](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L10)
```
    bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```
File AutoPxGlp.sol [line 18](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L18)
```
    uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
```
File: AutoPxGlp.sol [line 19](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L19)
```
    uint256 public constant MAX_PLATFORM_FEE = 2000;
```
File: AutoPxGlp.sol [line 20](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L20)
```
    uint256 public constant FEE_DENOMINATOR = 10000;
```
File: AutoPxGlp.sol [line 21](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L21)
```
    uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
```
File: AutoPxGlp.sol [line 22](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L22)
```
    uint256 public constant EXPANDED_DECIMALS = 1e30;
```
File: AutoPxGmx.sol [line 18](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L18)
```
    IV3SwapRouter public constant SWAP_ROUTER =
        IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45);
```
File: AutoPxGmx.sol [line 20](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L20)
```
    uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
```
File: AutoPxGmx.sol [line 21](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L21)
```
    uint256 public constant MAX_PLATFORM_FEE = 2000;
```
File: AutoPxGmx.sol [line 22](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L22)
```
    uint256 public constant FEE_DENOMINATOR = 10000;
```
File: AutoPxGmx.sol [line 23](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGmx.sol#L23)
```
    uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
```