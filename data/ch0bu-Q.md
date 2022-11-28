# LOW

## 1. `_safeMint()` should be used rather than `_mint()` wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

```
73       _mint(receiver, shares);
92       _mint(receiver, shares);
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol
```
50       _mint(to, amount);
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol


## 2. `require()` should be used instead of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

```
225      assert(feeAmount + postFeeAmount == assets);
```


## 3. `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
52	) ERC20(_name, _symbol, _asset.decimals()) {
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol


# NON-CRITICAL

# 1. The `nonReentrant modifier` should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

```
378	function depositGmx(uint256 amount, address receiver)
379		external
380		whenNotPaused
381		nonReentrant
382		returns (
...
422	function depositFsGlp(uint256 amount, address receiver)
423		external
424		whenNotPaused
425		nonReentrant
426		returns
...
554	function depositGlpETH(
555		uint256 minUsdg,
556		uint256 minGlp,
557		address receiver
558	)
559		external
560		payable
561		whenNotPaused
562		nonReentrant
563		returns (
...
583	function depositGlp(
584		address token,
585		uint256 tokenAmount,
586		uint256 minUsdg,
587		uint256 minGlp,
588		address receiver
589	)
590		external
591		whenNotPaused
592		nonReentrant
593		returns (
...
685	function redeemPxGlpETH(
686		uint256 amount,
687		uint256 minOut,
688		address receiver
689	)
690		external
691		whenNotPaused
692		nonReentrant
693		returns (
...
712	function redeemPxGlp(
713		address token,
714		uint256 amount,
715		uint256 minOut,
716		address receiver
717	)
718		external
719		whenNotPaused
720		nonReentrant
721		returns (
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol


## 2. NatSpec is incomplete

Check NatSpec rules:
https://docs.soliditylang.org/en/develop/natspec-format.html

```
Amost all contracts are missing @title, @author tags
```


## 3. Large multiples of ten should use scientific notation, for readability

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
44	uint256 public constant FEE_DENOMINATOR = 1_000_000;
47	uint256 public constant FEE_MAX = 200_000;
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol


## 4. Function order

Functions should be ordered following [the Solidity conventions](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions):

"Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private"


## 5. Avoid nested if statements
For better readability and analysis it is better to avoid nested `if` blocks.

```
838	if (token == address(pxGmx)) {
839		// Mint pxGMX for the user - the analog for esGMX rewards
840		pxGmx.mint(receiver, postFeeAmount);
841
842		if (feeAmount != 0) pxGmx.mint(address(pirexFees), feeAmount);
843	} else if (token == address(gmxBaseReward)) {
844		gmxBaseReward.safeTransfer(receiver, postFeeAmount);
845
846		if (feeAmount != 0)
847		gmxBaseReward.safeTransfer(address(pirexFees), feeAmount);
848	}
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexGmx.sol
```
251	if (newAssets != 0) {
252		totalPxGlpFee = (newAssets * platformFee) / FEE_DENOMINATOR;
253		pxGlpIncentive = optOutIncentive
254			? 0
255			: (totalPxGlpFee * compoundIncentive) / FEE_DENOMINATOR;
256
257		if (pxGlpIncentive != 0)
258			asset.safeTransfer(msg.sender, pxGlpIncentive);
259
260		asset.safeTransfer(owner, totalPxGlpFee - pxGlpIncentive);
261	}
...
266	if (pxGmxAmountOut != 0) {
267		// Calculate and distribute pxGMX fees if the amount of pxGMX increased
268		totalPxGmxFee = (pxGmxAmountOut * platformFee) / FEE_DENOMINATOR;
269		pxGmxIncentive = optOutIncentive
270			? 0
271			: (totalPxGmxFee * compoundIncentive) / FEE_DENOMINATOR;
272
273		if (pxGmxIncentive != 0)
274			pxGmx.safeTransfer(msg.sender, pxGmxIncentive);
275
276		pxGmx.safeTransfer(owner, totalPxGmxFee - pxGmxIncentive);
277
278		// Update the pxGmx reward accrual
279		_harvest(pxGmxAmountOut - totalPxGmxFee);
280	} else {
281		// Required to keep the globalState up-to-date
282		_globalAccrue();
283	}
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGlp.sol
```
288	if ((totalAssets() - assetsBeforeClaim) != 0) {
289		totalFee =
290			((asset.balanceOf(address(this)) - assetsBeforeClaim) *
291				platformFee) /
292			FEE_DENOMINATOR;
293		incentive = optOutIncentive
294			? 0
295			: (totalFee * compoundIncentive) / FEE_DENOMINATOR;
296
297		if (incentive != 0) asset.safeTransfer(msg.sender, incentive);
298
299		asset.safeTransfer(owner, totalFee - incentive);
300	}
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol
```
106	if (msg.sender != owner) {
107		uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
108
109		if (allowed != type(uint256).max)
110			allowance[owner][msg.sender] = allowed - shares;
111	}
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PirexERC4626.sol
```
115	if (globalRewards != 0 && userRewards != 0) {
116		// Update global and user reward states to reflect the claim
117		globalState.rewards = globalRewards - userRewards;
118		userRewardStates[msg.sender].rewards = 0;
119
120		// Transfer the proportionate reward token amounts to the recipient
121		uint256 _rewardState = rewardState;
122		uint256 amount = (_rewardState * userRewards) / globalRewards;
123
124		if (amount != 0) {
125			// Update reward state (i.e. amount) to reflect reward tokens transferred out
127			rewardState = _rewardState - amount;
128
129			pxGmx.safeTransfer(receiver, amount);
130
131			emit PxGmxClaimed(msg.sender, receiver, amount);
132		}
133	}
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol


## 6. Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

```
9	bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
10	bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
```
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol
