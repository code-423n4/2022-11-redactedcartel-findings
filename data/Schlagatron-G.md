General: 

What is the purpose of continuously checking for address(0)? It uses up gas in almost every function but appears to serve little to no purpose. In permissioned functions like [setProducer](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L93) that can be overwritten at any time, this check serves no purpose. In [setRewardToken](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L179): it checks for address(0) but doesn't check the index is within bounds (which is a more likely mistake) and either parameter being incorrect should cause no issues for funds or users. Then most importantly the non-permissioned functions only check address(0) but nothing else. What is the difference between a user passing in address(0) or address(1) etc. It seems to serve no purpose but added gas and complexity.
In PirexGmx [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L727) and [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L727) you are checking if the token is address(0) then checking its a valid token directly after which is completely redundant.

[PirexRewards](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol):

-Loops can be optimized by changing ++i to doing unchecked { i ++ } at the end of the loop, [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163), [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351) and [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L351).
-Instantiate variables outside of the loops to save gas [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L352-L356) and [here](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L397-L403).
-Allow a user to claim rewards without harvesting, harvesting is a gas intensive function and may want to be avoided if it was just recently called. Or at least to put a check on the last harvest so it is not harvested multiple times in the same block.

PxERC20/PirexRewards:

-Add a function in PirexRewards that can be called to both accrue Global and accrue an account in one call instead of having to make two separate calls to the same external contract in PxERC20 using one of the same variables.

Vaults:
-Multiple deposits in the same block will cause redundant compounding calls