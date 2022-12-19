### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 7*

the `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
238: for (uint256 i = 0; i < tokenIds.length; i++) {
258: for (uint256 i = 0; i < tokenIds.length; i++) {
468: for (uint256 i = 0; i < tokenIds.length; i++) {
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```
13: for (uint256 j = 0; j < 32; j++) {
22: for (uint256 j = 0; j < charCount; j++) {
33: for (uint256 i = 32; i < 64; i++) {
39: for (uint256 i = 0; i < charCount; i++) {
```


### G-02 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 3*

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
448: balanceOf[from] -= amount;
453: balanceOf[to] += amount;
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```
35: charCount += uint8(b[i]);
```


### G-03 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 2*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
463: function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```
87: function tokenName(address token) internal view returns (string memory) {
```


### G-04 DUPLICATED REQUIRE() or REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

*Number of Instances Identified: 1*

Saves deployment costs

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
343: require(caviar.owner() == msg.sender, "Close: not owner");
```


### G-05 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
71: require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```