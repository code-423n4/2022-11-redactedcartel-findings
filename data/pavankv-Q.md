
1. No check of Erc20 contract of given token address leads to failure of entire function:-
code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L651


2. Event is missing indexed fields :-

Each event should use three indexed fields if there are three or more fields 

code snippet :-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L27
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L50
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L56
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L39
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L44
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L22
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L29
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L36
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L97
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L104
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L115
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L125
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L133

3. Duplicate import of same file :-

code snippet :-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L6
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L8

4 . Missing check of Erc20 contract of given address :-

Add check whether it consist contract before going into further process.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L133
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L107

5. Use of Block.timestamp:-

Description

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

code snippet:-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L293
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L297
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L316
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L319
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L325

Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

