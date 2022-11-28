# [01] Lack of check for contract existence during ERC20 transfer

Solmate won't check for contract existence, see [comment](https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9).

When calling `PirexFeeds.distributeFees()`, if an incorrect erc20 tokens is passed (for a non existing contract), the function will result in a silent failure.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L100-L116

## Recommendationc

Add a check for contract existence. E.g. `if (address(token).code.length == 0) revert CustomError()` while using Solmate SafeTransferLib.

# [02] Missing storage gap for upgradeable contracts

Consider adding a `__gap[]` storage variable to allow for new storage variables in later versions.

See this [link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable issue. Please, refer to this [example](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol) for an implementation reference.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L15

# [03] Unbounded loop while iterating `rewardTokens`

## Proof of Concept

`PirexRewards.claim()` will iterate the state variable `p.rewardsTokens`

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L386-L387

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L396

New `rewardTokens` are pushed in `addRewardTokens()`, without a maximum size limit. 

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L169

## Impact

While calling `PirexRewards.claim()`, it's possible for the transaction to run out of gas if the amount of items in `p.rewardTokens()` is large.

## Recommendation

Add a maximum number of `rewardTokens` that can be added. Alternatively, add a startIndex and endIndex into `PirexRewards.claim()`, to allow claims to be made in chunks.

# [04] Front-runable initializer

There is nothing preventing another account from calling the initializer before the contract owner. In the best case, the owner is forced to waste gas and re-deploy. In the worst case, the owner does not notice that his/her call reverts, and everyone starts using a contract under the control of an attacker.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L85

# [05] Prevent `fees[f]` to receive a stale value

When calling `PirexGMX.setFee()`, if `fee` is equal to `fees[f]`, the protocol will unecessarily emit an event. Consider adding a check for `if(fee == fees[f]) revert CustomError()`.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L300-L306

# [06] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows internal above external

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L234-L235

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L272

# [07] Replace assert with require or custom error

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). "The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

Even if the code is expected to be unreacheable, consider using a custom error instead of assert.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L217-L226

Specifically for [L225](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225), a validation statement does not seem to be required as it's checking the calculation on [L223](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L223) (which is static) and `feeAmount` is an unsigned integer.

# [08] Inconsistent use of named return variables

Some functions return named variables, others return explicit values.

Following function returns an explicit value

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L378-L387

Following function returns a named variable

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L217-L220

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [09] Repeated validation logic can be converted into a function modifier

Converting repeated logic can improve code reusability.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L599-L600

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L727-L728

# [10] Missing NATSPEC

Consider adding NATSPEC on all external/public functions to improve documentation.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L315

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L339

# [11] Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L34-L41

# [12] Large multiples of ten should use scientific notation

Converting 10000 to 10e3 will improve code readability.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L22

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L20

# [13] The nonReentrant modifier should occur before all other modifiers

This is a best-practice to protect against re-entrancy in other modifiers. See previous [finding](https://code4rena.com/reports/2022-06-connext/#n-02-the-nonreentrant-modifier-should-occur-before-all-other-modifiers).

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L378-L381
