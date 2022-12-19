### [Low-01] Floating Pragma used

Solidity version should be stable and locked instead of floating

*Instances(4)*

```solidity
File:   Caviar.sol
File:   Pair.sol
File:   LpToken.sol
File:   SafeERC20Namer.sol
```

### [NC-01] Use of Magic Number

Instead of using magic number try to use constant state variable

*Instances(6)*

```solidity
File:   lib/SafeERC20Namer.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L11
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L55
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L66
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L69
```