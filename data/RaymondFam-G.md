## `uint256` Can be Cheaper Than `uint8` and Other Unsigned Integer Type of Smaller Bit Size
When dealing with function arguments or memory values, there is no inherent benefit because the compiler does not pack these values. Your contract’s gas usage may be higher because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. The EVM needs to properly enforce the limits of this smaller type.

It is only more efficient when you can pack variables of uint8, uint16, uint32, uint64, ... into the same storage slot with other neighboring variables smaller than 32 bytes. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);

83:    function setTreasuryFeePercent(uint8 _treasuryFeePercent)
```
## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
65:        onlyOwner

85:        onlyOwner
```
[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
48:        onlyRole(MINTER_ROLE)

65:        onlyRole(BURNER_ROLE)
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
104:    function setPoolFee(uint24 _poolFee) external onlyOwner {

116:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

128:    function setPlatformFee(uint256 fee) external onlyOwner {

140:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

152:    function setPlatform(address _platform) external onlyOwner {
```
[File: AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
94:    function setWithdrawalPenalty(uint256 penalty) external onlyOwner {

106:    function setPlatformFee(uint256 fee) external onlyOwner {

118:    function setCompoundIncentive(uint256 incentive) external onlyOwner {

130:    function setPlatform(address _platform) external onlyOwner {
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
93:    function setProducer(address _producer) external onlyOwner {

153:        onlyOwner

181:        onlyOwner

437:    ) external onlyOwner {

465:    ) external onlyOwner {
```
[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
272:    function configureGmxState() external onlyOwner whenPaused {

300:    function setFee(Fees f, uint256 fee) external onlyOwner {

315:        onlyOwner

741:        onlyPirexRewards

828:    ) external onlyPirexRewards {

865:    ) external onlyOwner {

884:    function setVoteDelegate(address voteDelegate) external onlyOwner {

895:    function clearVoteDelegate() public onlyOwner {

909:    function setPauseState(bool state) external onlyOwner {

924:        onlyOwner

959:        onlyOwner
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

As an example, the following `+=` instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90

```
            balanceOf[to] = balanceOf[to] + amount;
```
All other instances entailed:

[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
124:            balanceOf[to] += amount;
```
[File: PxGmxReward.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol)

```
95:            rewardState += rewardAmount;
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
                producerState.rewardStates[rewardTokens[i]] += r;
```
Similarly, as an example, the following `-=` instance entailed may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85

```
        balanceOf[msg.sender] = balanceOf[msg.sender] - amount;
```
All other instances entailed:

[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
119:        balanceOf[from] -= amount;
```
## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

As an example, the following instance of code block can refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80-L100

```
    function transfer(address to, uint256 amount)
        public
        override
        returns (bool status)
    {
        ...
        status = true;
    }
```
All other instances entailed:

[File: PxERC20.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol)

```
113:    ) public override returns (bool) {
```
[File: AutoPxGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```
164:    function totalAssets() public view override returns (uint256) {

177:        returns (uint256)

203:        returns (uint256)
```
[File: AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
142:    function totalAssets() public view override returns (uint256) {

155:        returns (uint256)

181:        returns (uint256)

333:        returns (uint256)

373:    ) external nonReentrant returns (uint256) {

417:    ) external payable nonReentrant returns (uint256) {
```
[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
232:        returns (uint256)

245:        returns (ERC20[] memory)

261:    ) external view returns (address) {
```
[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
237:        returns (uint256)

382:        returns (
383:            uint256,
384:            uint256,
385:            uint256
386:        )

426:        returns (
427:            uint256,
428:            uint256,
429:            uint256
430:        )

563:        returns (
564:            uint256,
565:            uint256,
566:            uint256
567:        )

593:        returns (
594:            uint256,
595:            uint256,
596:            uint256
597:        )

693:        returns (
694:            uint256,
695:            uint256,
696:            uint256
697:        )

721:        returns (
722:            uint256,
723:            uint256,
724:            uint256
725:        )
```
## `||` Costs Less Gas Than Its Equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

For instance, the `if conditional` instance below could be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L115

```
        if (!(globalRewards == 0 || userRewards == 0)) {
```
All other instances entailed:

[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
        if (globalRewards != 0 && userRewards != 0) {
```
## Use Storage Instead of Memory for Structs/Arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

Here are the instances entailed:

[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
160:       ERC20[] memory rewardTokens = p.rewardTokens;

216:        UserState memory userState = producerTokens[producerToken].userStates[

386:            ERC20[] memory rewardTokens = p.rewardTokens;
```
## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. 

When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` in the code block to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163-L167

```
        for (uint256 i; i < len;) {
            if (address(rewardTokens[i]) == address(rewardToken)) {
                revert TokenAlreadyAdded();
            }
            unchecked {
                ++i;
            }
        }
```
All other instances entailed:

[File: PirexRewards.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```
351:        for (uint256 i; i < pLen; ++i) {

396:            for (uint256 i; i < rLen; ++i) {
```
## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.13",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:
```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through a private (doesn't matter whether or not the contract entails any child contracts since the private visibility saves even more gas on function calls than the internal visibility) function to reduce contract size if need be.

For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L205-L208

```
    function _onlyPirexRewards() private view {
        if (msg.sender != pirexRewards) revert NotPirexRewards();
    }

    modifier onlyPirexRewards() {
        _onlyPirexRewards();
        _;
    }
```
## Ternary Over `if ... else`
Using ternary operator instead of the if else statement saves gas. 

For instance the following instance of code block may be rewritten as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L909-L915

```
    state
        ? {
            _pause();
        }
        : {
            _unpause();
        }
```
All other instances entailed:

[File: PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol)

```
241:        if (isBaseReward) {
...
243:        } else {

497:        if (token == address(0)) {
...
502:        } else {
```
[File: AutoPxGlp.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```
266:        if (pxGmxAmountOut != 0) {
...
280:        } else {
```