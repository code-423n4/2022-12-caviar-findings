1. Floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

[Caviar.sol#L2](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L2)
[LpToken.sol#L2](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L2)
[Pair.sol#L2](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L2)

Recommended mitigation steps:
Use fixed solidity version. (eg. : ```pragma solidity 0.8.17;```
