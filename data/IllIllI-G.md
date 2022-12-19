## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `internal` functions only called once can be inlined to save gas | 1 |  20 |
| [G&#x2011;02] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 |  85 |
| [G&#x2011;03] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 7 |  420 |
| [G&#x2011;04] | Optimize names to save gas | 2 |  44 |
| [G&#x2011;05] | Splitting `require()` statements that use `&&` saves gas | 1 |  3 |
| [G&#x2011;06] | Using `private` rather than `public` for constants, saves gas | 1 |  - |
| [G&#x2011;07] | Functions guaranteed to revert when called by normal users can be marked `payable` | 2 |  42 |

Total: 15 instances over 7 issues with **614 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There is 1 instance of this issue:*

```solidity
File: src/Pair.sol

463:      function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L463

### [G&#x2011;02]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*

```solidity
File: src/Pair.sol

/// @audit require() on line 157
168:              uint256 refundAmount = maxInputAmount - inputAmount;

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L168

### [G&#x2011;03]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 7 instances of this issue:*

```solidity
File: src/lib/SafeERC20Namer.sol

13:           for (uint256 j = 0; j < 32; j++) {

22:           for (uint256 j = 0; j < charCount; j++) {

33:           for (uint256 i = 32; i < 64; i++) {

39:           for (uint256 i = 0; i < charCount; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/lib/SafeERC20Namer.sol#L13

```solidity
File: src/Pair.sol

238:          for (uint256 i = 0; i < tokenIds.length; i++) {

258:          for (uint256 i = 0; i < tokenIds.length; i++) {

468:          for (uint256 i = 0; i < tokenIds.length; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L238

### [G&#x2011;04]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 2 instances of this issue:*

```solidity
File: src/Caviar.sol

/// @audit create(), destroy()
12:   contract Caviar is Owned {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Caviar.sol#L12

```solidity
File: src/Pair.sol

/// @audit add(), remove(), buy(), sell(), wrap(), unwrap(), nftAdd(), nftRemove(), nftBuy(), nftSell(), close(), baseTokenReserves(), fractionalTokenReserves(), price(), buyQuote(), sellQuote(), addQuote(), removeQuote()
16:   contract Pair is ERC20, ERC721TokenReceiver {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L16

### [G&#x2011;05]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There is 1 instance of this issue:*

```solidity
File: src/Pair.sol

71:           require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L71

### [G&#x2011;06]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There is 1 instance of this issue:*

```solidity
File: src/Pair.sol

25:       bytes32 public immutable merkleRoot;

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L25

### [G&#x2011;07]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 2 instances of this issue:*

```solidity
File: src/LpToken.sol

19:       function mint(address to, uint256 amount) public onlyOwner {

26:       function burn(address from, uint256 amount) public onlyOwner {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/LpToken.sol#L19


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `<array>.length` should not be looked up in every loop of a `for`-loop | 3 |  9 |
| [G&#x2011;02] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 8 |  40 |
| [G&#x2011;03] | Using `private` rather than `public` for constants, saves gas | 2 |  - |
| [G&#x2011;04] | Use custom errors rather than `revert()`/`require()` strings to save gas | 16 |  - |

Total: 29 instances over 4 issues with **49 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 3 instances of this issue:*

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
238:          for (uint256 i = 0; i < tokenIds.length; i++) {

/// @audit (valid but excluded finding)
258:          for (uint256 i = 0; i < tokenIds.length; i++) {

/// @audit (valid but excluded finding)
468:          for (uint256 i = 0; i < tokenIds.length; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L238

### [G&#x2011;02]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 8 instances of this issue:*

```solidity
File: src/lib/SafeERC20Namer.sol

/// @audit (valid but excluded finding)
13:           for (uint256 j = 0; j < 32; j++) {

/// @audit (valid but excluded finding)
17:                   charCount++;

/// @audit (valid but excluded finding)
22:           for (uint256 j = 0; j < charCount; j++) {

/// @audit (valid but excluded finding)
33:           for (uint256 i = 32; i < 64; i++) {

/// @audit (valid but excluded finding)
39:           for (uint256 i = 0; i < charCount; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/lib/SafeERC20Namer.sol#L13

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
238:          for (uint256 i = 0; i < tokenIds.length; i++) {

/// @audit (valid but excluded finding)
258:          for (uint256 i = 0; i < tokenIds.length; i++) {

/// @audit (valid but excluded finding)
468:          for (uint256 i = 0; i < tokenIds.length; i++) {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L238

### [G&#x2011;03]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 2 instances of this issue:*

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
20:       uint256 public constant ONE = 1e18;

/// @audit (valid but excluded finding)
21:       uint256 public constant CLOSE_GRACE_PERIOD = 7 days;

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L20

### [G&#x2011;04]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 16 instances of this issue:*

```solidity
File: src/Caviar.sol

/// @audit (valid but excluded finding)
30:           require(pairs[nft][baseToken][merkleRoot] == address(0), "Pair already exists");

/// @audit (valid but excluded finding)
51:           require(msg.sender == pairs[nft][baseToken][merkleRoot], "Only pair can destroy itself");

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Caviar.sol#L30

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
71:           require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

/// @audit (valid but excluded finding)
74:           require(baseToken == address(0) ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");

/// @audit (valid but excluded finding)
80:           require(lpTokenAmount >= minLpTokenAmount, "Slippage: lp token amount out");

/// @audit (valid but excluded finding)
117:          require(baseTokenOutputAmount >= minBaseTokenOutputAmount, "Slippage: base token amount out");

/// @audit (valid but excluded finding)
120:          require(fractionalTokenOutputAmount >= minFractionalTokenOutputAmount, "Slippage: fractional token out");

/// @audit (valid but excluded finding)
151:          require(baseToken == address(0) ? msg.value == maxInputAmount : msg.value == 0, "Invalid ether input");

/// @audit (valid but excluded finding)
157:          require(inputAmount <= maxInputAmount, "Slippage: amount in");

/// @audit (valid but excluded finding)
189:          require(outputAmount >= minOutputAmount, "Slippage: amount out");

/// @audit (valid but excluded finding)
224:          require(closeTimestamp == 0, "Wrap: closed");

/// @audit (valid but excluded finding)
343:          require(caviar.owner() == msg.sender, "Close: not owner");

/// @audit (valid but excluded finding)
361:          require(caviar.owner() == msg.sender, "Withdraw: not owner");

/// @audit (valid but excluded finding)
364:          require(closeTimestamp != 0, "Withdraw not initiated");

/// @audit (valid but excluded finding)
367:          require(block.timestamp >= closeTimestamp, "Not withdrawable yet");

/// @audit (valid but excluded finding)
470:              require(isValid, "Invalid merkle proof");

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L71
