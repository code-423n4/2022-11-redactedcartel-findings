# 1. [G-1] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File src/PirexRewards.sol, line 163:     for (uint256 i; i < len; ++i)
    File src/PirexRewards.sol, line 351:     for (uint256 i; i < pLen; ++i)
    File src/PirexRewards.sol, line 396:     for (uint256 i; i < rLen; ++i)

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 2. [G-2] Variables: "Constant" expressions are expressions, not constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Instances include:

    File src/PxERC20.sol, line 9:               bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
    File src/PxERC20.sol, line 10:             bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");

Change these expressions from `constant` to `immutable` and implement the calculation in the constructor, or hardcode these values in the constants and add a comment to say how the value was calculated.

# 3. [G-3] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

There are 4 instances of this issue:

    File src/PirexRewards.sol, line 361:               producerState.rewardStates[rewardTokens[i]] += r;

    File src/PxERC20.sol, line 85:               balanceOf[msg.sender] -= amount;
    File src/PxERC20.sol, line 90:               balanceOf[to] += amount;
    File src/PxERC20.sol, line 119:               balanceOf[from] -= amount;
    File src/PxERC20.sol, line 124:               balanceOf[to] += amount;

    File src/vaults/PxGmxReward.sol, line 95:           rewardState += rewardAmount;