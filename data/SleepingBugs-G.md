## Increments can be `unchecked` in loops
### Summary
Unchecked operations as the `++i` on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/SafeERC20Namer.sol#L17
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/SafeERC20Namer.sol#L22
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/SafeERC20Namer.sol#L39

### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header





## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/LpToken.sol#L19
    function mint(address to, uint256 amount) public onlyOwner {

https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/LpToken.sol#L26
    function burn(address from, uint256 amount) public onlyOwner {


### Mitigation
Consider adding payable to save gas




## `>=` cheaper than `>`
### Summary
Strict inequalities ( `>` ) are more expensive than non-strict ones ( `>=` ). This is due to some supplementary checks (`ISZERO`, 3 gas)

### Details
It's important to consider whether incrementing/decrementing one of the operators could result in an over/underflow.

This optimization is applicable when the optimizer is turned off.

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L80
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L117
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L120
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L157
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L189
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L367

### Mitigation
Consider using `>= 1` instead of `> 0` to avoid some opcodes
Same solution with other values from `x >= y` to `x > y+1` or `x-1 > y`



## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L453
            balanceOf[to] += amount;
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L448
        balanceOf[from] -= amount;
### Mitigation
Don't use `+=` for state variables as it cost more gas.


## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
    function close() public {
        // check that the sender is the caviar owner
        require(caviar.owner() == msg.sender, "Close: not owner");

        // set the close timestamp with a grace period
        closeTimestamp = block.timestamp + CLOSE_GRACE_PERIOD;

        // remove the pair from the Caviar contract
        caviar.destroy(nft, baseToken, merkleRoot);

        emit Close(closeTimestamp);
    }
```

Recommendation
Change to:

```
    function close() public {
        // check that the sender is the caviar owner
        require(caviar.owner() == msg.sender, "Close: not owner");

        // set the close timestamp with a grace period
        uint _closeTimestamp = block.timestamp + CLOSE_GRACE_PERIOD; // @audit
        closeTimestamp = _closeTimestamp; // @audit

        // remove the pair from the Caviar contract
        caviar.destroy(nft, baseToken, merkleRoot);

        emit Close(_closeTimestamp); // @audit
    }
```
### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L346-L351

### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas


## duplicated `require()` check should be refactored
### Summary
duplicated `require()` / `revert()` checks should be
refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L74
        require(baseToken == address(0) ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L151
        require(baseToken == address(0) ? msg.value == maxInputAmount : msg.value == 0, "Invalid ether input");
### Mitigation
refactor this checks to different functions to save gas




## splitting `require()` statements that use `&&` saves gas
### Summary
Instead of using the && operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving 3 gas per & ):
### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L71

### Mitigation
Split require statements

## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks
https://github.com/code-423n4/2022-12-caviar/blob/039095ee6b73709289d88cb99397e8b9028224c7/src/Pair.sol#L463
    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {


### Mitigation
Consider changing internal function only called once to inline code for gas savings


