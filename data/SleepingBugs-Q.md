
## Emitted amount can be bigger than expected
### Impact 
There are ERC20 tokens with transfer at fees. For checking if the transferred amount is the same as expected, code already compares balanceOf before and balanceOf after transfer. People can get confused in cases where real value doesn't match, also applications like subgraphs that uses this value won't work as expected.

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L93-L99
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L136-L141
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L171-L175
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L202-L206
### Mitigation
Consider implementing a system like:
```
        uint256 balanceBefore = _token.balanceOf(address(this));
        _token.safeTransferFrom(_from, address(this), _amount);
        uint256 balanceAfter = _token.balanceOf(address(this));

        // check / control flow when (balanceAfter - balanceBefore != _amount);
```
### Recommendation
Consider comparing before and after balance to get the actual transferred amount.


## Missing checks on `mint` can lead to burn tokens
### Summary
When minting LP tokens, 0 address is not being checked and therefore can be burned the amount

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/d3061461ca3f39330f791c0503ece2c657c8413d/src/LpToken.sol#L20

### Mitigation
Check zero address before assigning or using it


## `block.timestamp` used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L346
        closeTimestamp = block.timestamp + CLOSE_GRACE_PERIOD;

https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L367
        require(block.timestamp >= closeTimestamp, "Not withdrawable yet");





### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks


https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L399
        return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);

https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L408
        return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);


### Mitigation
Replace magic hardcoded numbers with declared constants.


## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.17. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Caviar.sol#L2
pragma solidity ^0.8.17;

https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L2
pragma solidity ^0.8.17;

https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/LpToken.sol#L2
pragma solidity ^0.8.17;

https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/lib/SafeERC20Namer.sol#L2
pragma solidity ^0.8.17;

### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^0.8.17 into 0.8.17





## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 

### Details 
Contracts has partial or full lack of comments

### Github Permalinks 
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L1

 ### Mitigation
 - Add Natspec descriptors like `@param` or `@return`

