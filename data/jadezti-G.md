## Gas Optimizations

|         | Issue                                                        | Instances |
| ------- |:------------------------------------------------------------ |:---------:|
| [GAS-1] | Using `unchecked` math operations when no overflow/underflow |     10     |

### [GAS-1] Using `unchecked` math operations when no overflow/underflow

C4 report has optimized for-loops. There is a further optimization `unchecked index increment` can be applied since the looping times is limited within `loop length` which means it's safe to use unchecked operation.
The optimized for-loop:
```solidity
for(uint i; i<length;){
  // ...operations...
  unchecked {++i;}  //@audit: using unchecked increment saves gas.
}
```

*unchecked math operations for for-loops in File: src/Pair.sol*
```solidity
File: src/Pair.sol

238:         for (uint256 i = 0; i < tokenIds.length; i++) {

258:         for (uint256 i = 0; i < tokenIds.length; i++) {

468:         for (uint256 i = 0; i < tokenIds.length; i++) {
```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)

*unchecked math operations for for-loops in File: src/lib/SafeERC20Namer.sol*
```solidity
File: src/lib/SafeERC20Namer.sol

13:         for (uint256 j = 0; j < 32; j++) {

22:         for (uint256 j = 0; j < charCount; j++) {

33:         for (uint256 i = 32; i < 64; i++) {

39:         for (uint256 i = 0; i < charCount; i++) {
```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib/SafeERC20Namer.sol)

In below code, the `//@audit ...` annotation explains why `unchecked` operation can be applied.

*Instances (1): L17*
```solidity
File: src/lib/SafeERC20Namer.sol

12    uint256 charCount = 0;
13    for (uint256 j = 0; j < 32; j++) {

17:     charCount++;  //@audit - see L12-L13, `charCount` starts from `0` and increment within a limited for-loop.
```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib/SafeERC20Namer.sol)

*Instances (2): L157, L479*
```solidity
FIle: src/Pair.sol

157     require(inputAmount <= maxInputAmount, "Slippage: amount in");

168:    uint256 refundAmount = maxInputAmount - inputAmount;  //@audit - see L157

    
479:    ? address(this).balance - msg.value         //@audit - msg.value is included in the contract balance, so no underflow here.
        : ERC20(baseToken).balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

