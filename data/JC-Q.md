
---

## Summary

- N-01 Event is missing indexed fields | 14 instances
- N-02 Large multiples of ten should use scientific notation | 12 instances

- L-01 require() should be used instead of assert() | 1 instance

Total: 27 instances in 3 issues

---

## N-01 Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

Instances (14):

PirexGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L86-L94
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L07-L103
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L125-L132
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L133-L139

PirexRewards.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L50-L55
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L56-L62
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L63-L67

PirexFees.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L36-L41

vaults/AutoPxGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L44-L54

vaults/AutoPxGlp.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L39-L49

vaults/PxGmxReward.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L22-L27
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L29-L33

vaults/PirexERC4626.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L27-L32



## N-02 Large multiples of ten should use scientific notation 

Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability

Instances (12)

PirexGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L44
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L47

vaults/AutoPxGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L22
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L23
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L29
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L30

vaults/AutoPxGlp.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L19
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L20
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L21
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L25
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L26

 


## L-01 require() should be used instead of assert()

Instance (1):

PirexGmx.sol
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L225

