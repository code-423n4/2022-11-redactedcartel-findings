## Un-indexed Parameters in Events
Consider indexing parameters for events, serving as logs filter when looking for specifically wanted data. Up to three parameters in an event function can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data.

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient);
35:    event SetTreasuryFeePercent(uint8 _treasuryFeePercent);
```
## Complementary Codehash Checks
Consider adding an optional codehash check for addresses at the constructor since the zero address check cannot guarantee a matching address has been inputted.

For instance, the following code block may be refactored as follows:

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol#L50-L56

```
    constructor(address _treasury, address _contributors, bytes32 _treasuryCodeHash, bytes32 _contributorsCodeHash) Owned(msg.sender) {
        if (_treasury == address(0) || _treasury.codehash != _treasuryCodeHash) revert ZeroAddress();
        if (_contributors == address(0) || _contributors.codehash != _contributorsCodeHash) revert ZeroAddress();

        treasury = _treasury;
        contributors = _contributors;
    }
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability. 

Here are the instances entailed:

[File: PirexFees.sol](https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexFees.sol)

```
34:    event SetFeeRecipient(FeeRecipient f, address recipient); // @ f

63:    function setFeeRecipient(FeeRecipient f, address recipient) // @ f

69:        emit SetFeeRecipient(f, recipient); // @ f

71:        if (f == FeeRecipient.Treasury) { // @ f
```