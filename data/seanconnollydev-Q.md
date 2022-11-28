Consider using trailing commas for constructor and function arguments. See [Avoiding Naming Collisions](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#avoiding-naming-collisions) from the Solidity Style Guide.

Example: [PxGMX constructor arguments](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L168)

```
    constructor(
        address pxGmx_,
        address pxGlp_,
        address pirexFees_,
        address pirexRewards_,
        address delegateRegistry_,
        address gmxBaseReward_,
        address gmx_,
        address esGmx_,
        address gmxRewardRouterV2_,
        address stakedGlp_
    ) Owned(msg.sender) {
    ...
    }
```