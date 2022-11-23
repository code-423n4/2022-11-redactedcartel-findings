**PirexFees.sol**
- L51/52/67 - Use assembly to check for address(0), Saves 6 gas per instance.

- L104 - The distribution - treasuryDistribution operation can be unchecked since in L102 when treasuryDistribution is defined it follows the following invariant: "X <= distribution", therefore underflow would never occur.


**PirexGmx.sol**
- L182-191/317/389/433/495/497/599/630/727/829/831/885/926/961 - Use assembly to check for address(0), Saves 6 gas per instance.

- L223 - The assets - feeAmount operation can be unchecked since in L222 when feeAmount is defined it follows the following invariant: "X <= assets", therefore underflow would never occur.


**PirexRewards.sol**
- L94/112/113/114/136/137/155/156/183/271/282/283/374/375/439/440/441/467/468 - Use assembly to check for address(0), Saves 6 gas per instance.


**src/vaults/PxGmxReward.sol**
- L41/69/106 - Use assembly to check for address(0), Saves 6 gas per instance.

- L126 - The _rewardState - amount operation can be unchecked since in L121 and L122 when defining amount it follows the following invariant: "X <= _rewardState", therefore underflow would never occur.


**src/vaults/PirexERC4626.sol**
- L68/137 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**src/vaults/AutoPxGmx.sol**
- L82/83/84/87/88/153/376 - Use assembly to check for address(0), Saves 6 gas per instance.


**src/vaults/AutoPxGlp.sol**
- L75/76/79/80/131/336/374/378/421 - Use assembly to check for address(0), Saves 6 gas per instance.
