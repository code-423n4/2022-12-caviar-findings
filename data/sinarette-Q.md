### [NC-1] Typo in `_validateTokenIds()`, `bytes23` > `bytes32`

*Instances (1)*:
```solidity
File: src/Pair.sol

465:        if (merkleRoot == bytes23(0)) return;
```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)