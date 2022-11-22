#### 1.Functions that can be declared external
`public` functions never called by the contract should be declared `external` to save gas.
Solidity immediately copies array arguments to memory in public functions, while external functions can read directly from the calldata. Memory allocation is expensive, whereas reading from calldata is cheap.

**Path:** ./external/DelegateRegistry.sol : setDelegate(), clearDelegate()
./external/RewardTracker.sol : claimable()
**Recommendation:** Use external instead of public

#### 2. Redundant Use of SafeMath
Since Solidity v0.8.0, the overflow/underflow check is implemented via `ABIEncoderV2` on the language level - it adds the validation to the bytecode during compilation.
There is no need to use the SafeMath library, by removing it Gas consumption would be reduced.
**Path:** ./external/RewardTracker.sol
**Recommendation:** Remove `SafeMath` library from the project.

#### 3. Presence of unused variables
Unused variables are allowed in Solidity and they do not pose a direct security issue. It is best practice though to avoid them as they can:
* cause an increase in computations (and unnecessary gas consumption)
* indicate bugs or malformed data structures and they are generally a sign of poor code quality
* cause code noise and decrease the readability of the code

**Path:** ./external/RewardTracker.sol : BASIS_POINTS_DIVISOR, 
**Recommendation:** Remove unused variables.
