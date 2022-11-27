## 01. PirexRewards need a constructor to init owner and disable Initializers

PirexRewards is OwnableUpgradeable and Initializable, but the implementation contract is not initialized in constructor. Anyone can who calling `initialize()` first will become the owner.

It should have a proper constructor as its mentioned in Initializable.sol:

  > Avoid leaving a contract uninitialized.
  >
  > An uninitialized contract can be taken over by an attacker. This applies to both a proxy and its implementation
  > contract, which may impact the proxy. To prevent the implementation contract from being used, you should invoke
  > the {_disableInitializers} function in the constructor to automatically lock it when it is deployed:

Should add a constructor to PirexRewards.sol:
  ```
  constructor() {
      _disableInitializers();
  }
  ```

## 02. Missing afterDeposit() in AutoPxGmx.depositGmx()

`AutoPxGmx.depositGmx()` should call `afterDeposit(receiver, assets, shares);` just like `deposit()` and `mint()` in the base contract - PirexERC4626.

Although `afterTransfer()` is an empty function now, it is logically correct to maintain this consistency and reduce the probability of bug in future contract updates.

## 03. Missing beforeWithdraw() and afterWithdraw() in withdraw() and redeem() of AutoPxGmx

AutoPxGmx should call `beforeWithdraw()` and `afterWithdraw()` in `withdraw()` and `redeem()`.
Although `beforeWithdraw()` and `afterWithdraw()` are empty functions now, it is logically correct to call them.

The simplier way is call `PirexERC4626.withdraw(assets, receiver, owner);` and `PirexERC4626.redeem(shares, receiver, owner);` instead,
just like [`AutoPxGlp.withdraw`](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L443) and [`AutoPxGlp.redeem`](https://github.com/code-423n4/2022-11-redactedcartel/blob/03b71a8d395c02324cb9fdaf92401357da5b19d1/src/vaults/AutoPxGlp.sol#L456) did.

