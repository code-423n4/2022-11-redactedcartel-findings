## MISSING CHECK ON TRANSFER FUNCTION

### Description:

In the transfer function https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80-L91  , 
there should be a check like `amount< balanceOf[msg.sender]` which would revert with an appropriate error string  so that
in cases where the user mistakenly gives a higher value to `amount` then he is prompted with a appropriate error.

## EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

### Description:

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use
the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they
should each be used in their appropriate contexts. constants should be used for literal values written into the code, and
immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L10

## PUBLIC FUNCTIONS NOT BEING CALLED WITHIN THE CONTRACT SHOULD BE LABELLED EXTERNAL INSTEAD

### Description:

Public functions that are not called within the contract should be labelled as external instead to maintain readability throughout the codebase.

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L109
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L315
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L339
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L177
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L151

## INTERNAL FUNCTION SHOULD BEGIN WITH AN UNDERSCORE

### Description:

As a good coding practice , the internal functions should be named beginning with an underscore.

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L462
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L474
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L487
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L501
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L222

## LACK OF PROPER COMMENTS

### Description:

There are instances throughout the code base where areas such as function lack comments which makes code readability more tough . 
Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L19
This struct lacks comments about its constituents
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L315
This withdraw function lacks comments about the parameters of the function.
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L68-L75
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L15-L31

## LACK OF ZERO ADDRESS CHECKS

### Description:

Lack of zero address check on functions like transfer functions can be dangerous as , if the `to` address is mistakenly set to a 0 address , 
it might lead to loss of funds as the funds would be transferred to the 0-address.

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L80
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L110
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L111

## EVENT IS MISSING INDEXED FIELDS

### Description:

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index
field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields).
Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for
the events in question. If there are fewer than three fields, all of the fields should be indexed.

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L34
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L35
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L37
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L22
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L28
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L29
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L49
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L50
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L56
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L63
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L33
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L39-L44
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L35-L40
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L86
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L95
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L125
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L133
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L140-L143


## INCONSISTENCY IN CODE IN _globalAccrue AND _userAccrue FUNCTIONS

### Description:

In the _globalAccrue function  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L49rewards is calculated by `uint256 rewards = globalState.rewards +
            (block.timestamp - globalState.lastUpdate) *
            globalState.lastSupply;` whereas in _userAccrue function https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L68 it is calculated as ` uint256 rewards = u.rewards +
            u.lastBalance *
            (block.timestamp - u.lastUpdate);` 
They should be made consistent with each other(The order in which the formula is put)

## THE FUNCTION setTreasuryFeePercent LACKS 0 CHECK

### Description:

The function here https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L83 lets the owner set the 
treasury fee percent , but this function lacks the check to see if the owner mistakenly sets the fee to 0.
Cause if he/she sets the fee to 0 then no fees/rewards would be sent to the treasury disrupting the flow of the logic.
Add a check something like `require(fee != 0 )`

## LACK OF CHECK ON WITHDRAW FUNCTION FOR SUFFICIENT ALLOWANCE

### Description:

In the function here https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L326 it sets the allowance
to the variable named allowed  , but does not check if the allowance is sufficient for the transfer to go through  , a user should know why his transaction reverted via a require statement or revert statement.
Add a check like `allowance[owner][msg.sender] >= shares`
This will also prevent a abrupt revert on  https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L329


