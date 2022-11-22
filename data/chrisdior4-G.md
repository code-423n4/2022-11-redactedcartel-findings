# [G-01] x += y COSTS MORE GAS THAN x= x + y FOR STATE VARIABLES

Using the addition operator instead of plus-equals saves 113 gas

File: `PirexRewards.sol`

producerState.rewardStates\[rewardTokens\[i\]\] += r;

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L361

File: `PxERC20.sol`

balanceOf\[msg.sender\] -= amount;

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L85

balanceOf\[to\] += amount;

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L90

balanceOf\[from\] -= amount;

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L119

balanceOf\[to\] += amount;

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PxERC20.sol#L124

# [G-02]. ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Consider wrapping with an unchecked block here (around 25 gas saved per instance):

File: `PirexRewards.sol`

for (uint256 i; i < len; ++i) {

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L163

for (uint256 i; i < pLen; ++i) {

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L351

for (uint256 i; i < rLen; ++i) {

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexRewards.sol#L396

# [G-03] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost:

There are multiple functions containing onlyOwner modifier in:

- src/vaults/AutoPxGmx.sol
- src/vaults/AutoPxGlp.sol
- src/PirexFees.sol
- src/PirexGmx.sol
- src/PirexRewards.sol


