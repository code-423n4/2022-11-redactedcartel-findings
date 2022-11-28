# [G] USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

instances:
```solidity
File: AutoPxGlp.sol
18:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
19:     uint256 public constant MAX_PLATFORM_FEE = 2000;
20:     uint256 public constant FEE_DENOMINATOR = 10000;
21:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;
22:     uint256 public constant EXPANDED_DECIMALS = 1e30;

File: AutoPxGmx.sol
18:     IV3SwapRouter public constant SWAP_ROUTER =
19:         IV3SwapRouter(0x68b3465833fb72A70ecDF485E0e4C7bD8665Fc45);
20:     uint256 public constant MAX_WITHDRAWAL_PENALTY = 500;
21:     uint256 public constant MAX_PLATFORM_FEE = 2000;
22:     uint256 public constant FEE_DENOMINATOR = 10000;
23:     uint256 public constant MAX_COMPOUND_INCENTIVE = 5000;

File: PirexFees.sol
20:     uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
21: 
22:     // Maximum treasury fee percent
23:     uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;

File: PirexGmx.sol
43:     // Fee denominator
44:     uint256 public constant FEE_DENOMINATOR = 1_000_000;
45: 
46:     // Fee maximum (i.e. 20%)
47:     uint256 public constant FEE_MAX = 200_000;

File: PxERC20.sol
09:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
10:     bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```

# [G] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

instances:
```solidity
File: PirexFees.sol
20:     uint8 public constant FEE_PERCENT_DENOMINATOR = 100;
23:     uint8 public constant MAX_TREASURY_FEE_PERCENT = 75;
28:     uint8 public treasuryFeePercent = MAX_TREASURY_FEE_PERCENT;

```


# [G] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

Using the addition operator instead of plus-equals saves 113 gas

instances:
```
File: PirexRewards.sol
361:                 producerState.rewardStates[rewardTokens[i]] += r;

File: PxGmxReward.sol
95:             rewardState += rewardAmount;

File: PxERC20.sol
90:             balanceOf[to] += amount;

```


# [G] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

instances:
```
File: PirexRewards.sol
163:         for (uint256 i; i < len; ++i) {
351:         for (uint256 i; i < pLen; ++i) {
396:         for (uint256 i; i < rLen; ++i) {
```


# [G] Emitting block.timestamp is not necessary 

`block.timestamp` and `block.number` are added to event information by default so adding them manually wastes gas

instances:
```solidity
File: PxGmxReward.sol
61:         emit GlobalAccrue(block.timestamp, totalSupply, rewards);

83:         emit UserAccrue(user, block.timestamp, balance, rewards);

```
