### Summary

Overall the codebase is quite readable, well organized and matched well with the specs described in the documenations. Its make use of several best practices like using up to date solidity version (0.8.17) and organized structure when its comes to delcaration of state variable, events and functions. Variable naming for the most part is quite good, considering the complexity of the protocol. Comments were used rigourously and were beneficial and accurate in describing the object they were comenting on.
   
   

### Non-Critical issues
#### [NC-01] No Indexed Field for Event

instances:1

File: PirexRewards.sol
Line: 63
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```javascript
event Harvest(
        ERC20[] producerTokens,
        ERC20[] rewardTokens,
        uint256[] rewardAmounts
    );
```

#### [NC-02]

Could include conditional check similar to _globalaccrue() function to update when needed only.
if (block.timestamp != lastUpdate || balance != lastbalance){}

instances:1

File: PirexRewards.sol
Line: 289
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol)

```javascript
 // Calculate the amount of rewards accrued by the user up to this call
uint256 rewards = u.rewards +
       u.lastBalance *
        (block.timestamp - u.lastUpdate);
```

#### [NC-03] Renaming Variable for Improved Readbility

Consider renaming assets to assetAmount for clearer readablity, as assets implicity portrays an ERC20 types

instances:4

File: AutoPxGlp.sol
Line: 436-440
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```javascript
 function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public override returns (uint256 shares) {
```


File: AutoPxGlp.sol
Line: 449-453
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol)

```javascript
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public override returns (uint256 assets) {
```

File: AutoPxGmx.sol
Line: 315-319
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```javascript
 function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public override returns (uint256 shares) {
```

File: AutoPxGmx.sol
Line: 339-343
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```javascript
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public override returns (uint256 assets) {
```

#### [NC-04] Missing Comments for Functions

Missing comments for function, adding comments will add more clarity for this function

instances:2

File: AutoPxGmx.sol
Line: 315-319
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```javascript
 function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public override returns (uint256 shares) {
```

File: AutoPxGmx.sol
Line: 339-343
 [Link](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol)

```javascript
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public override returns (uint256 assets) {
```