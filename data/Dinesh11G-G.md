
### Don't Initialize Variables with Default Value

#### Impact
Issue Information: Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2022-11-redactedcartel/::818 => for (uint256 i = 0; i < _depositTokens.length; i++) {
```
#### Tools used
Manual



=================================================================================================


### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
2022-11-redactedcartel/src/PirexGmx.sol::873 => if (d.length == 0) revert EmptyString();
2022-11-redactedcartel/src/PirexRewards.sol::161 => uint256 len = rewardTokens.length;
2022-11-redactedcartel/src/PirexRewards.sol::187 => uint256 lastIndex = rewardTokens.length - 1;
2022-11-redactedcartel/src/PirexRewards.sol::348 => uint256 pLen = _producerTokens.length;
2022-11-redactedcartel/src/PirexRewards.sol::387 => uint256 rLen = rewardTokens.length;
2022-11-redactedcartel/src/PirexRewards.sol::438 => if (lpContract.code.length == 0) revert NotContract();
2022-11-redactedcartel/src/PirexRewards.sol::466 => if (lpContract.code.length == 0) revert NotContract();
2022-11-redactedcartel/src/PxERC20.sol::31 => if (bytes(_name).length == 0) revert EmptyString();
2022-11-redactedcartel/src/PxERC20.sol::32 => if (bytes(_symbol).length == 0) revert EmptyString();
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::77 => if (bytes(_name).length == 0) revert InvalidAssetParam();
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::78 => if (bytes(_symbol).length == 0) revert InvalidAssetParam();
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::85 => if (bytes(_name).length == 0) revert InvalidAssetParam();
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::86 => if (bytes(_symbol).length == 0) revert InvalidAssetParam();
```
#### Tools used
Manual


=================================================================================================



### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
2022-11-redactedcartel/::127 => require(b > 0, errorMessage);
2022-11-redactedcartel/::290 => return size > 0;
2022-11-redactedcartel/::494 => if (returndata.length > 0) {
2022-11-redactedcartel/::623 => if (returndata.length > 0) {
2022-11-redactedcartel/::1040 => if (tokenAmount > 0) {
2022-11-redactedcartel/::1131 => require(_amount > 0, "RewardTracker: invalid _amount");
2022-11-redactedcartel/::1159 => require(_amount > 0, "RewardTracker: invalid _amount");
2022-11-redactedcartel/::1191 => if (supply > 0 && blockReward > 0) {
2022-11-redactedcartel/::1222 => if (_claimableReward > 0 && stakedAmounts[_account] > 0) {
127 => if (multiplier > 0) {
```
#### Tools used
Manual


=================================================================================================



### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: ⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2022-11-redactedcartel/src/PxERC20.sol::9 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2022-11-redactedcartel/src/PxERC20.sol::10 => bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```
#### Tools used
Manual

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
2022-11-redactedcartel/src/PirexFees.sol::5 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/PirexGmx.sol::4 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-11-redactedcartel/src/PirexGmx.sol::6 => import {Pausable} from "openzeppelin-contracts/contracts/security/Pausable.sol";
2022-11-redactedcartel/src/PirexGmx.sol::7 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/PirexGmx.sol::11 => import {DelegateRegistry} from "";
2022-11-redactedcartel/src/PirexGmx.sol::12 => import {IRewardRouterV2} from "";
2022-11-redactedcartel/src/PirexGmx.sol::16 => import {IRewardDistributor} from "";
2022-11-redactedcartel/src/PirexRewards.sol::4 => import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
2022-11-redactedcartel/src/PirexRewards.sol::5 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/PxERC20.sol::5 => import {AccessControl} from "openzeppelin-contracts/contracts/access/AccessControl.sol";
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::6 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::7 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::8 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/vaults/AutoPxGlp.sol::9 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::4 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::7 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/vaults/AutoPxGmx.sol::8 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::5 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
2022-11-redactedcartel/src/vaults/PirexERC4626.sol::6 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
2022-11-redactedcartel/src/vaults/PxGmxReward.sol::5 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
```
#### Tools used
Manual


=================================================================================================



### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2022-11-redactedcartel/::219 => * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
2022-11-redactedcartel/::302 => * https://diligence.consensys.net/posts/2019/09/stop-using-soliditys-transfer-now/[Learn more].
2022-11-redactedcartel/src/mocks/PirexRewardsMock.sol::16 => return producerTokens[producerToken].rewardStates[rewardToken] * 2;
```
#### Tools used
Manual