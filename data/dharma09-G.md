## 1.OR CONDITIONS  COST  LESS  THAN  THEIR  EQUIVALENT, AND CONDITIONS (“NOT(SOMETHING  IS  FALSE)” COSTS  LESS  THAN “EVERYTHING  IS  TRUE”)  
the equivalent of `(a && b)` is ` !(!a || !b)`  
  
Even with the 10k Optimizer enabled, `OR` conditions  cost  less  than  their  equivalent `AND` conditions.  
  
### Proof of Concept.  
Compare in Remix this example contract’s 2 diffs (or any test contract of your choice, as experimentation always shows the same results).  
```  
pragma solidity 0.8.13;  
contract Test {  
bool isOpen;  
bool channelPreviouslyOpen;  
  
function boolTest() external view returns (uint) {  
- if (isOpen && !channelPreviouslyOpen) {  
+ if (!(!isOpen || channelPreviouslyOpen)) {  
return 1;  
- } else if (!isOpen && channelPreviouslyOpen) {  
+ } else if (!(isOpen || !channelPreviouslyOpen)) {  
return 2;  
}  
}  
function setBools(bool _isOpen, bool _channelPreviouslyOpen) external {  
isOpen = _isOpen;  
channelPreviouslyOpen= _channelPreviouslyOpen;  
}  
}  
```  
effectively saving 12 gas.  
  
### Affected Code  
It’s possible to save a significant amount of gas by replacing the `&&` conditions with their `||` equivalent in the solution.
[PirexRewards.sol#L385](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L385)
[PxGmxReward.sol#L115](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L115)

### MITIGATION

Use `if !(globalRewards ==  0  || userRewards ==  0)`instead of `if (globalRewards !=  0  && userRewards !=  0)`

### 2.Constant expressions are [re-calculated each time it is in use](https://github.com/ethereum/solidity/issues/9232), costing an extra `97` gas than a constant every time they are called.

### PROOF OF CONCEPT
Instances include:
### MITIGATION
Mark these as  `immutable`  instead of  `constant`
[PxERC20.sol#](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L9)
```
#L9:    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
#L10:   bytes32  public constant BURNER_ROLE =  keccak256("BURNER_ROLE");
```
### 3. functions called by only the owner it's should be declared external instead of public                             

[PirexGmx.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol#L895)
```
#L895 : function clearVoteDelegate() public onlyOwner {
```
### MITIGATION
Mark these as  `external`  instead of  `public`