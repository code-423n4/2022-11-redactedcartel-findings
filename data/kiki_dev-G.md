Gas OP


[1] x += y cost more gas. You can use x = x + y instead.
Although it is easier to change the value of a variable by using += or -=. It cost more gas than the written out version x = x + y and x = x - y.

There are 6 instances of this issue:

PxERC20.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85
ln 85, 90, 119, 124

PirexRewards.sol 
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L361
ln 361

PxGmxRewards.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95
ln 95


[2] Functions that are only accessible by owner can be marked as payable to save on gas. 

Becasue the function will revert when called by anyone besides the owner due to modifiers such as onlyowner. Gas can be saved by marking those functions as payable. Marking the function as payable will lower the gas cost for legitimate callers 
because the compiler will not include checks for whether a payment was provided.
This will save cost whenever the function is called, as well as during deployment.

There are 25 instances of this issue:

PirexRewards.sol 
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93
ln 93, 153, 181, 437, 465

PirexGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L272
ln 272, 300, 315, 865, 884, 895, 909, 924, 959

PirexFees.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L65
ln 65, 85

AutoPxGlp.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L94
ln 94, 106, 118, 130

AutoPxGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L104
ln 104, 116, 128, 140, 152


[3] ++i/i++ in loops should be unchecked. If there is no possibility of under/over flow.

The unchecked keyword is new in solidity version 0.8.0. Often times in loops the 'i' is just used to index data structures. 
In these instances where there is no math inside of the loop, it is okay to have these loops unchecked. This will save gas on every iteration.

There is 1 instances of this issue:

PirexRewards.sol 
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163
ln 163
