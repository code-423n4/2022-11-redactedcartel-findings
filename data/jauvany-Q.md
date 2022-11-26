Different pragma solidity

Vulnerability details

PirexERC4626.sol has a different pragma statement than the rest, it contains an additional ">=".

It's cleaner to use the same solidity versions.

Proof of Concept 

Common.sol: pragma solidity 0.8.17;
PirexFees.sol: pragma solidity 0.8.17;
PirexGmx.sol: pragma solidity 0.8.17;
PirexRewards.sol: pragma solidity 0.8.17;
PxERC20.sol: pragma solidity 0.8.17;
PxGmx.sol: pragma solidity 0.8.17;
IAutoPxGlp.sol: pragma solidity 0.8.17;
IPirexRewards.sol: pragma solidity 0.8.17;
IProducer.sol: pragma solidity 0.8.17;
AutoPxGlp.sol: pragma solidity 0.8.17;
AutoPxGmx.sol: pragma solidity 0.8.17;
PirexERC4626.sol: pragma solidity >=0.8.0;
PxGmxReward.sol: pragma solidity 0.8.17;

Recommended Mitigation Steps

Use the same solidity versions