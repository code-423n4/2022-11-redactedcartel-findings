1 .Use latest solidity version and lock to one version in all files:-

description :-
Because old version might have bugs so update to latest version>

code snippet :-
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L2

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