## [NAZ-L] Assert Violation That Always Incorrect Control Flow Implementation
**Severity**: Low
**Context**: [`PirexGmx.sol#L225`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225)

**Description**:
The Solidity assert() function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

1). A bug exists in the contract that allows it to enter an invalid state;
2). The assert statement is used incorrectly, e.g. to validate inputs.

**Recommendation**:
Consider whether the condition checked in the assert() is actually an invariant. If not, replace the assert() statement with a require() statement. If the exception is indeed caused by unexpected behaviour of the code, fix the underlying bug(s) that allow the assertion to be violated.


## [NAZ-L] Max/Infinite Approvals are Dangerous
**Severity**: Low
**Context**: [`PirexGmx.sol#L292`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L292), [`PirexGmx.sol#L353`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L353), [`AutoPxGlp.sol#L87`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L87), [`AutoPxGmx.sol#L96`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L96)

**Description**:
Giving max/infinite approvals to contracts are dangerous because if those contracts are exploited then they can remove all the funds from the approving addresses.

**Recommendation**:
Consider checking allowance and approve as much as required.


## [NAZ-L] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`PirexFees.sol#L63`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L63), [`PirexFees.sol#L83`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83), [`PirexGmx.sol#L272`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L272), [`PirexGmx.sol#L300`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300), [`PirexGmx.sol#L313`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L313), [`PirexGmx.sol#L862`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L862), [`PirexGmx.sol#L884`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L884), [`PirexRewards.sol#L93`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93), [`PirexRewards.sol#L151`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L151), [`PirexRewards.sol#L179`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L179), [`PirexRewards.sol#L432`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L432), [`PirexRewards.sol#L461`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L461), [`AutoPxGlp.sol#L94`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94), [`AutoPxGlp.sol#L106`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106), [`AutoPxGlp.sol#L118`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118), [`AutoPxGlp.sol#L130`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130), [`AutoPxGmx.sol#L104`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104), [`AutoPxGmx.sol#L116`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116), [`AutoPxGmx.sol#L128`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128), [`AutoPxGmx.sol#L140`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140), [`AutoPxGmx.sol#L152`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L] Missing Time locks
**Severity**: Low 
**Context**: [`PirexFees.sol#L63`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L63), [`PirexFees.sol#L83`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83), [`PirexGmx.sol#L272`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L272), [`PirexGmx.sol#L300`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300), [`PirexGmx.sol#L313`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L313), [`PirexGmx.sol#L862`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L862), [`PirexGmx.sol#L884`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L884), [`PirexRewards.sol#L93`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93), [`PirexRewards.sol#L151`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L151), [`PirexRewards.sol#L179`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L179), [`PirexRewards.sol#L432`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L432), [`PirexRewards.sol#L461`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L461), [`AutoPxGlp.sol#L94`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94), [`AutoPxGlp.sol#L106`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L106), [`AutoPxGlp.sol#L118`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L118), [`AutoPxGlp.sol#L130`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L130), [`AutoPxGmx.sol#L104`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104), [`AutoPxGmx.sol#L116`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L116), [`AutoPxGmx.sol#L128`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L128), [`AutoPxGmx.sol#L140`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L140), [`AutoPxGmx.sol#L152`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L152)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a timelock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorised owners have no time for any planned incident response.


## [NAZ-L] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`PirexRewards.sol#L85`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L85)

**Description**:
None of the initialize functions emit emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`PirexERC4626.sol#L46`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L46)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`AutoPxGlp.sol#L462`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L462), [`AutoPxGlp.sol#L474`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L474), [`AutoPxGlp.sol#L487`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L487), [`AutoPxGlp.sol#L501`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L501), [`AutoPxGmx.sol#L222`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L222), [`PirexERC4626.sol#L272`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L272), [`PirexERC4626.sol#L278`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L278), [`PirexERC4626.sol#L284`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L284), [`PirexERC4626.sol#L290`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L290), [`PirexERC4626.sol#L296`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L296)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`PxGmx.sol#L12`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L12), [`PxGmx.sol#L23`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxGmx.sol#L23), [`PirexERC4626.sol#L276`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L276), [`PirexERC4626.sol#L282`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L282), [`PirexERC4626.sol#L288`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L288), [`PirexERC4626.sol#L294`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L294), [`PirexERC4626.sol#L300`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L300)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N] Unindexed Event Parameters
**Severity** Informational
**Context**: [`PxGmxReward.sol#L21`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`AutoPxGlp.sol#L19-L21`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L19-L21), [`AutoPxGlp.sol#L25-L26`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L25-L26), [`AutoPxGmx.sol#L21-L26`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L21-L26), [`AutoPxGmx.sol#L29-L30`](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L29-L30)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-11-redactedcartel)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.