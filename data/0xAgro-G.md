# Gas Report
## Finding Summary
||Issue|Instances|User Savings (total avg)|Deployment Savings (total avg)|
|-|-|-|-|-|
|[G-01]|Value Not Pre-Calculated|1|5028|0
|[G-02]|`uint256` Iterator Checked Each Iteration|7|3379|68
|[G-03]|`x += y` Used For State Variables|1|466|0

### [G-01] Value Not Pre-Calculated

Readability can be sacrificed for user gas savings. The value `160 - 4 * 4` can be pre-calculated to `144` and inlined to save gas.

#### Findings:

*/src/lib/SafeERC20Namer.sol*
Links: [55](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L55).
```solidity
55:	return Strings.toHexString(uint160(token) >> (160 - 4 * 4));
```
**Suggested Change**
```solidity
55:	return Strings.toHexString(uint160(token) >> (144));
```

### [G-02] `uint256` Iterator Checked Each Iteration

A `uint256` iterator will not overflow before the check variable overflows. Unchecking the iterator increment saves gas.
**Example**
```solidity
//From
for (uint256 i; i < len; ++i) {
	//Do Something
}
//To
for (uint256 i; i < len;) {
	//Do Something
	unchecked { ++i; }
}
```

#### Findings:

*/src/Pair.sol*
Links: [238](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238), [258](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258), [468](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468).
```solidity
238:	for (uint256 i = 0; i < tokenIds.length; i++) {
258:	for (uint256 i = 0; i < tokenIds.length; i++) {
468:	for (uint256 i = 0; i < tokenIds.length; i++) {
```

*/src/lib/SafeERC20Namer.sol*
Links: [13](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13), [22](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22), [33](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33), [39](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39).
```solidity
13:	for (uint256 j = 0; j < 32; j++) {
22:	for (uint256 j = 0; j < charCount; j++) {
33:	for (uint256 i = 32; i < 64; i++) {
39:	for (uint256 i = 0; i < charCount; i++) {
```


### [G-03] `x += y` Used

`x = x + y` is cheaper than `x += y` in some cases like below.

#### Findings:

*/src/lib/SafeERC20Namer.sol*
Links: [35](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L35).
```solidity
35:	charCount += uint8(b[i]);
```
**Suggested Change**
```solidity
35:	charCount = charCount + uint8(b[i]);
```