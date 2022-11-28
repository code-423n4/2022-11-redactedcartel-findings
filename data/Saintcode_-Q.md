## EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

2 Instances:
-src\PxERC20.sol lines 9, 10


## LACK OF REENTRANCY GUARDS

Whenever IERC1155(token).safeTransferFrom is called, the to address can re-enter back to the contracts due to the ERC1155TokenReceiver(to).onERC1155Received(msg.sender, from, id, amount, data) code (hook)

Instances:
-src\PirexGmx.sol lines 844, 847, 946
-src\vaults\AutoPxGlp.sol line 210
-src\vaults\AutoPxGlp.sol lines 242, 315
-src\vaults\PirexERC4626.sol line 119, 145
-src\vaults\PxGmxReward.sol line 128


## ARRAY LENGTH NOT VALIDATED

Function did not validate that the length of two parameter arrays is the same. If the array length is different, it might cause unexpected behavior.

-src\PirexRewards.sol line 338

## AVOID NESTED IF BLOCKS
For better readability and analysis it is better to avoid nested if blocks. 


## FLOATING SOLIDITY PRAGMA VERSION
It’s best to use the same compiler version across all project files/team members. So having a fixed version pragma is a good practice. 
Contract `PirexERC4626.sol` uses a floating pragma which would allow the patch number to be equal or higher than the specified patch number.









