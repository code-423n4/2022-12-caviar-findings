# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i = 0; i < tokenIds.length; i++) {``` [L238](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238) 
1. ```for (uint256 i = 0; i < tokenIds.length; i++) {``` [L258](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258) 
1. ```for (uint256 i = 0; i < tokenIds.length; i++) {``` [L468](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468) 
1. ```for (uint256 j = 0; j < 32; j++) {``` [L13](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13) 
1. ```for (uint256 j = 0; j < charCount; j++) {``` [L22](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22) 
1. ```for (uint256 i = 32; i < 64; i++) {``` [L33](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33) 
1. ```for (uint256 i = 0; i < charCount; i++) {``` [L39](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```uint256 refundAmount = maxInputAmount - inputAmount;``` [L168](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L168) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
