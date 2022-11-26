Different pragma solidity

Vulnerability details

PirexERC4626.sol has a different pragma statement than the rest, it contains an additional ">=".

Recommended Mitigation Steps

Use the same solidity versions