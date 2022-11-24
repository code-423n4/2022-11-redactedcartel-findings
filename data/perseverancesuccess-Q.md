# Low severity Bug 1: State variable owner shaddowing might make users confuse or lead to vulnerability 

The owner is a critical global state varible in Ownable contract 

solmate/auth/Owned.sol
```
address public owner;

```
So the functions use the parameter with the same name owner might lead to confusion and vulnerability.  
Should rename the parameter name to avoid shadowing , e.g. rename to _owner



https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L315-L318

```
function withdraw(
        uint256 assets,
        address receiver,
        address owner 
    ) public override returns (uint256 shares)

```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L339-L343
```
function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public override returns (uint256 assets) 

```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L99-L103
```
function withdraw(
        uint256 assets,
        address receiver,
        address owner
    ) public virtual returns (uint256 shares) 
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L124-L128
```
 function redeem(
        uint256 shares,
        address receiver,
        address owner
    ) public virtual returns (uint256 assets) {
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L225-L231
```

function maxWithdraw(address owner) public view virtual returns (uint256) {
        return convertToAssets(balanceOf[owner]);
    }

    function maxRedeem(address owner) public view virtual returns (uint256) {
        return balanceOf[owner];
    }
```

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol#L272-L301
```
function beforeWithdraw(
        address owner,
        uint256 assets,
        uint256 shares
    ) internal virtual {}

    function beforeDeposit(
        address receiver,
        uint256 assets,
        uint256 shares
    ) internal virtual {}

    function afterWithdraw(
        address owner,
        uint256 assets,
        uint256 shares
    ) internal virtual {}

    function afterDeposit(
        address receiver,
        uint256 assets,
        uint256 shares
    ) internal virtual {}

    function afterTransfer(
        address owner,
        address receiver,
        uint256 amount
    ) internal virtual {}

```