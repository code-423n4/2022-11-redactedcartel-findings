# [N-01] Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

event InitiateMigration(address newContract);
event CompleteMigration(address oldContract);
event SetVoteDelegate(address voteDelegate);

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L140
- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L141
- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L143


# [N-02] The nonReentrant modifier should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers. 
Seen in the following two functions:

File: `PirexGmx.sol`


function depositGmx(uint256 amount, address receiver)
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )
    {

- https://github.com/code-423n4/2022-11-redactedcartel/blob/74af1b6efe4c0c3f4796e0899a6c37fc745abdd7/src/PirexGmx.sol#L378

function depositFsGlp(uint256 amount, address receiver)
        external
        whenNotPaused
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256
        )

- https://github.com/code-423n4/2022-11-redactedcartel/blob/74af1b6efe4c0c3f4796e0899a6c37fc745abdd7/src/PirexGmx.sol#L422

# [L-01] Require should be used instead of Assert

Solidity documents mention that properly functioning code should never reach a failing assert statement
and if this happens there is a bug in the contract which should be fixed.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require

assert(feeAmount + postFeeAmount == assets);

- https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/PirexGmx.sol#L225

