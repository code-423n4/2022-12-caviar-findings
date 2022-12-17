## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `internal` functions only called once can be inlined to save gas | 1 | 20 |
| [G&#x2011;02] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 | 85 |
| [G&#x2011;03] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 8 | 480 |
| [G&#x2011;04] | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 | 84 |
| [G&#x2011;05] | State variables should be cached in stack variables rather than re-reading them from storage | 1 | 97 |
| [G&#x2011;06] | Splitting `require()` statements that use `&&` saves gas | 1 | 3 |


Total: 16 instances over 6 issues with **769 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There is 1 instance of this issue:*
```solidity
File: src/Pair.sol

463:      function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#463

### [G&#x2011;02]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*
```solidity
File: src/Pair.sol

/// @audit require() on line 157
168:              uint256 refundAmount = maxInputAmount - inputAmount;

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#168

### [G&#x2011;03]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 8 instances of this issue:*
```solidity
File: src/Pair.sol

238:          for (uint256 i = 0; i < tokenIds.length; i++) {

258:          for (uint256 i = 0; i < tokenIds.length; i++) {

468:          for (uint256 i = 0; i < tokenIds.length; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#238

```solidity
File: src/lib/SafeERC20Namer.sol

13:           for (uint256 j = 0; j < 32; j++) {

/// @audit for-loop on line 13
17:                   charCount++;

22:           for (uint256 j = 0; j < charCount; j++) {

33:           for (uint256 i = 32; i < 64; i++) {

39:           for (uint256 i = 0; i < charCount; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#238

### [G&#x2011;04]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*
```solidity
File: src/LpToken.sol

19:       function mint(address to, uint256 amount) public onlyOwner {

26:       function burn(address from, uint256 amount) public onlyOwner {

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#19

```solidity
File: src/Pair.sol

/// @audit require() on line 343
341:      function close() public {

/// @audit require() on line 361
359:      function withdraw(uint256 tokenId) public {

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#341

### [G&#x2011;05]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There is 1 instance of this issue:*
```solidity
File: src/Pair.sol

/// @audit closeTimestamp on line 364
367:          require(block.timestamp >= closeTimestamp, "Not withdrawable yet");

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#367

### [G&#x2011;06]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There is 1 instance of this issue:*
```solidity
File: src/Pair.sol

71:           require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#71
