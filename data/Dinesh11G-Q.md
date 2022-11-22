### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

Example
🤦 Bad:

IERC20(token).transferFrom(msg.sender, address(this), amount);
🚀 Good (using OpenZeppelin's SafeERC20):

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
🚀 Good (using require):

bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");

#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::436 => stakedGlp.transferFrom(msg.sender, address(this), amount);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::243 => bool status = ERC20.transfer(to, amount);
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::261 => bool status = ERC20.transferFrom(from, to, amount);
```
#### Tools used
Manual


=================================================================================================


### Unspecific Compiler Version Pragma

#### Impact
Issue Information: Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::2 => pragma solidity >=0.8.0;
```
#### Tools used
Manual

### Do not use Deprecated Library Functions

#### Impact
Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::292 => gmx.safeApprove(address(stakedGmx), type(uint256).max);
2022-11-redactedcartel/src/PirexGmx.sol::348 => gmx.safeApprove(address(stakedGmx), 0);
2022-11-redactedcartel/src/PirexGmx.sol::353 => gmx.safeApprove(contractAddress, type(uint256).max);
2022-11-redactedcartel/src/PirexGmx.sol::507 => t.safeApprove(glpManager, tokenAmount);
2022-11-redactedcartel/src/PxERC20.sol::37 => _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
2022-11-redactedcartel/::552 => function safeApprove(
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::87 => gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::347 => stakedGlp.safeApprove(platform, amount);
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::391 => erc20Token.safeApprove(platform, tokenAmount);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::96 => gmxBaseReward.safeApprove(address(SWAP_ROUTER), type(uint256).max);
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::97 => gmx.safeApprove(_platform, type(uint256).max);
```
#### Tools used
Manual