# Report


## Low Risk and Non-Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Spelling Error | 1 |
| [NC-1](#NC-1) | Unnecessary Redendent Function baseTokenReserves() and _baseTokenReserves() | 1 |

###  [L-1] Spelling Error
Used bytes23 in place of bytes32

*Instances (1)*:
```solidity
File: src/Pair.sol

465:             if (merkleRoot == bytes23(0)) return;

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)

###  [NC-1] Unnecessary Redendent Function baseTokenReserves() and _baseTokenReserves()
Implementation Logic of _baseTokenReserves() Function can directly be written in baseTokenReserves() Function.

*Instances (1)*:
```solidity
File: src/Pair.sol

379:             function baseTokenReserves() public view returns (uint256) {
380:                   return _baseTokenReserves();
381:             }

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)

