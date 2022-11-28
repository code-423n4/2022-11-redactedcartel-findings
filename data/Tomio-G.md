1.
Title: Expression for `constant` values such as a call to `keccak256()`, should use `immutable` rather than `constant`

Proof of Concept:
[PxERC20.sol#L9-L10](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9-L10)

Recommended Mitigation Steps:
Change from `constant` to `immutable`
reference: [here](https://github.com/ethereum/solidity/issues/9232)
________________________________________________________________________

2.
Title: `>=` is cheaper than `>`

Impact:
Strict inequalities (`>`) are more expensive than non-strict ones (`>=`). This is due to some supplementary checks (ISZERO, 3 gas)

Proof of Concept:
[PirexGmx.sol#L252](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L252)

Recommended Mitigation Steps:
Consider using `>=` instead of `>` to avoid some opcodes
________________________________________________________________________