# typo  

In https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L465 function _validateTokenIds
```solidity
if (merkleRoot == bytes23(0)) return;
```
should be 
```solidity
if (merkleRoot == bytes32(0)) return;
```
There are maybe a typo, bug it doesn't affect security.