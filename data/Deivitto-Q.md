
# QA

# Low

## Single-step process for critical ownership transfer/renounce is risky
### Impact
Given that `PirexRewards` is derived from `OwnableUpgradeable`, the ownership management of this contract defaults to `Ownable` ’s `transferOwnership()` and `renounceOwnership()` methods which are not overridden here. 

Such critical address transfer/renouncing in one-step is very risky because it is irrecoverable from any mistakes

Scenario: If an incorrect address, e.g. for which the private key is not known, is used accidentally then it prevents the use of all the `onlyOwner()` functions forever, which includes the changing of various critical addresses and parameters. This use of incorrect address may not even be immediately apparent given that these functions are probably not used immediately. 

When noticed, due to a failing `onlyOwner()` function call, it will force the redeployment of these contracts and require appropriate changes and notifications for switching from the old to new address. This will diminish trust in the protocol and incur a significant reputational damage.


### Github Permalinks
- `OwnableUpgradeable`
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L15
contract PirexRewards is OwnableUpgradeable {


### Proof of Concept
See similar High Risk severity finding from Trail-of-Bits Audit of Hermez.
https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf
See similar Medium Risk severity finding from Trail-of-Bits Audit of Uniswap V3:
https://github.com/Uniswap/v3-core/blob/main/audits/tob/audit.pdf

### Recommended steps
Recommend overriding the inherited methods to null functions and use separate functions for a two-step address change:
1. Approve a new address as a `pendingOwner`
2. A transaction from the `pendingOwner` address claims the pending ownership change.

This mitigates risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the address/ownership change.

Also, consider adding a time-delay for such sensitive actions. And at a minimum, use a multisig owner address and not an EOA.




## Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions
### Impact 
For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments" (quote OpenZeppelin). Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts, potentially causing loss of user fund or cause the contract to malfunction completely.

See:
https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
For a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

### Proof of Concept
In the following context of the upgradeable contracts they are expected to use gaps for avoiding collision:
- `OwnableUpgradeable`

However, none of these contracts contain storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Refer to the bottom part of this article:

https://docs.openzeppelin.com/contracts/4.x/upgradeable

If a contract inheriting from a base contract contains additional variable, then the base contract cannot be upgraded to include any additional variable, because it would overwrite the variable declared in its child contract. This greatly limits contract upgradeability.

### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L15
contract PirexRewards is OwnableUpgradeable {

### Tools Used
Manual analysis

### Recommended Mitigation Steps
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. 
Please reference OpenZeppelin upgradeable contract templates.

`uint256[50] private __gap;`





## Missing checks for address(0x0) when assigning values to `address` `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/PirexERC4626.sol#L48-L54

### Mitigation
Check zero address before assigning or using it


## Use of `asserts()`
### Impact

From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
You have reachable asserts in the following locations (which should be replaced by `require` / are mistakenly left from development phase):

### Details
The Solidity `assert()` function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

A bug exists in the contract that allows it to enter an invalid state;
The assert statement is used incorrectly, e.g. to validate inputs.

### References
SWC ID: 110

### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L217-L226

### Recommended Mitigation Steps
Substitute `asserts` with `require`/`revert`.




## `block.timestamp` used for calculating rewards
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116
### Github Permalinks 
- for calculating rewards

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/PxGmxReward.sol#L54
            (block.timestamp - globalState.lastUpdate) *
timestamp.safeCastTo32();

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/PxGmxReward.sol#L77
            (block.timestamp - u.lastUpdate);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L291
            (block.timestamp - u.lastUpdate);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L316
                (block.timestamp - lastUpdate) *






### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L85
    function initialize() public initializer {



### Mitigation
Use the constructor to initialize non-proxied contracts.

For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.


# Informational
## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L34
    event SetFeeRecipient(FeeRecipient f, address recipient);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexFees.sol#L35
    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/PxGmxReward.sol#L21
    event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/PxGmxReward.sol#L28
    event Harvest(uint256 rewardAmount);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L39
    event PoolFeeUpdated(uint24 _poolFee);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L40
    event WithdrawalPenaltyUpdated(uint256 penalty);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L41
    event PlatformFeeUpdated(uint256 fee);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L42
    event CompoundIncentiveUpdated(uint256 incentive);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGmx.sol#L43
    event PlatformUpdated(address _platform);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L35
    event WithdrawalPenaltyUpdated(uint256 penalty);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L36
    event PlatformFeeUpdated(uint256 fee);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L37
    event CompoundIncentiveUpdated(uint256 incentive);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/vaults/AutoPxGlp.sol#L38
    event PlatformUpdated(address _platform);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexRewards.sol#L33
    event SetProducer(address producer);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L140
    event InitiateMigration(address newContract);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L141
    event CompleteMigration(address oldContract);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L142
    event SetDelegationSpace(string delegationSpace, bool shouldClear);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L143
    event SetVoteDelegate(address voteDelegate);

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L144
    event ClearVoteDelegate();

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.




## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Value `1_000_000` can be used in scientific notation
### Github Permalinks

https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PirexGmx.sol#L44
    uint256 public constant FEE_DENOMINATOR = 1_000_000;

### Mitigation
Consider using scientific corresponding notation

## Empty blocks should emit something or revert
### Github Permalinks
https://github.com/code-423n4/2022-11-redactedcartel/blob/684627b7889e34ba7799e50074d138361f0f532b/src/PxGmx.sol#L23
    {}

### Mitigation
Revert or emit something.

