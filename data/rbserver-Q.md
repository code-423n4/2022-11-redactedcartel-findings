## [L-01] INCORRECT COMMENT
Incorrect comment can be misleading. The following `gmxBaseReward.safeApprove` code does not approve the Uniswap V3 router so the comment is not correct. For better readability and maintainability, please update the comment to match the code.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L86-L87
```solidity
        // Approve the Uniswap V3 router to manage our base reward (inbound swap token)
        gmxBaseReward.safeApprove(address(_platform), type(uint256).max);
```

## [N-01] REPEATED AND REDUNDANT CODE CAN BE REMOVED
`SafeTransferLib` is imported twice in `vaults\AutoPxGlp.sol`. Please remove one of these `import` code lines.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol#L6-L8
```solidity
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
...
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
```


## [N-02] MISSING `indexed` EVENT FIELDS
Querying events can be optimized with indices. Please consider adding `indexed` to the relevant fields of the following events.

```solidity
PirexFees.sol
  34: event SetFeeRecipient(FeeRecipient f, address recipient);   

PirexGmx.sol
  125: event ClaimRewards(   
  142: event SetDelegationSpace(string delegationSpace, bool shouldClear);   

vaults\PxGmxReward.sol
  21: event GlobalAccrue(uint256 lastUpdate, uint256 lastSupply, uint256 rewards);    
```

## [N-03] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The `@return` comments are missing for the following functions. Please consider completing the NatSpec comments for them.

```solidity
vaults\PirexERC4626.sol
  238: function transfer(address to, uint256 amount)
  256: function transferFrom(
```

## [N-04] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.

```solidity
PirexRewards.sol
  85: function initialize() public initializer {  

vaults\AutoPxGmx.sol
  315: function withdraw(  
  339: function redeem(  

vaults\PirexERC4626.sol
  60: function deposit(uint256 assets, address receiver)  
  80: function mint(uint256 shares, address receiver)  
  99: function withdraw(  
  124: function redeem(  
  154: function totalAssets() public view virtual returns (uint256);  
  156: function convertToShares(uint256 assets)    
  167: function convertToAssets(uint256 shares)    
  178: function previewDeposit(uint256 assets)  
  187: function previewMint(uint256 shares) public view virtual returns (uint256) {  
  193: function previewWithdraw(uint256 assets)    
  204: function previewRedeem(uint256 shares)  
  217: function maxDeposit(address) public view virtual returns (uint256) {  
  221: function maxMint(address) public view virtual returns (uint256) {  
  225: function maxWithdraw(address owner) public view virtual returns (uint256) {  
  229: function maxRedeem(address owner) public view virtual returns (uint256) {  
```