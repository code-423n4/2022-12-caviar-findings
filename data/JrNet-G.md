## [G001] >= costs less gas than >

he compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

#### Instances:

File: Pair.sol
```solidity
169 if (refundAmount > 0) msg.sender.safeTransferETH(refundAmount);
419 => if (lpTokenSupply > 0) {
```

File: SafeERC20Namer.sol
```solidity
2022-12-caviar/src/lib/SafeERC20Namer.sol::69 => } else if (data.length > 64) {
```
## [G002] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

#### Instances:

File: Pair.sol
```solidity
448 balanceOf[from] -= amount;
453 balanceOf[to] += amount;
```

File: SafeERC20Namer.sol
```solidity
35 charCount += uint8(b[i]);
```
## [G003] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### Instances:

File: Pair.sol
```solidity
238 => for (uint256 i = 0; i < tokenIds.length; i++) {
258 => for (uint256 i = 0; i < tokenIds.length; i++) {
468 => for (uint256 i = 0; i < tokenIds.length; i++) {
```

File: SafeERC20Namer.sol
```solidity
13 for (uint256 j = 0; j < 32; j++) {
22 for (uint256 j = 0; j < charCount; j++) {
33 for (uint256 i = 32; i < 64; i++) {
39 for (uint256 i = 0; i < charCount; i++) {
```

## [G004] Splitting `require()` statements that use `&&` saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

#### Instances:

File: Pair.sol
```solidity
71 require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

## [G005] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)

#### Instances:

File: Caviar.sol
```solidity
21 constructor() Owned(msg.sender) {}
```

File: LpToken.sol
```solidity
14 {}
```