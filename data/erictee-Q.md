### [L-01] ```approve()``` and ```safeApprove()``` should be replaced with ```safeIncreaseAllowance()``` / ```safeDecreaseAllowance()```


#### Impact
```approve()``` & ```safeApprove()``` are deprecated and subject to a known front-running attack. Consider using  ```safeIncreaseAllowance()``` & ```safeDecreaseAllowance()``` instead.


#### Findings:
```
src/PirexGmx.sol:L292                 gmx.safeApprove(address(stakedGmx), type(uint256).max);

src/PirexGmx.sol:L348                     gmx.safeApprove(address(stakedGmx), 0);

src/PirexGmx.sol:L353                     gmx.safeApprove(contractAddress, type(uint256).max);

src/PirexGmx.sol:L507                     t.safeApprove(glpManager, tokenAmount);

src/vaults/AutoPxGlp.sol:L87                 gmxBaseReward.safeApprove(address(_platform), type(uint256).max);

src/vaults/AutoPxGlp.sol:L347                 stakedGlp.safeApprove(platform, amount);

src/vaults/AutoPxGlp.sol:L391                     erc20Token.safeApprove(platform, tokenAmount);

src/vaults/AutoPxGmx.sol:L96                 gmxBaseReward.safeApprove(address(SWAP_ROUTER), type(uint256).max);

src/vaults/AutoPxGmx.sol:L97                 gmx.safeApprove(_platform, type(uint256).max);

```

### [L-02] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
src/PirexGmx.sol:L225        assert(feeAmount + postFeeAmount == assets);

```

### [L-03] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
src/vaults/PirexERC4626.sol:L52    ) ERC20(_name, _symbol, _asset.decimals()) {

```

### [L-04] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
src/vaults/PirexERC4626.sol:L2     pragma solidity >=0.8.0;

```

### [L-05] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
src/PxERC20.sol:L50        _mint(to, amount);

src/vaults/PirexERC4626.sol:L73        _mint(receiver, shares);

src/vaults/PirexERC4626.sol:L92        _mint(receiver, shares);

src/vaults/AutoPxGlp.sol:L317        _mint(receiver, shares);

src/vaults/AutoPxGmx.sol:L400        _mint(receiver, shares);

```
