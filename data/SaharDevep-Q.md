# c4udit Report

## Files analyzed
- examples\Caviar.sol
- examples\LpToken.sol
- examples\Pair.sol

## Issues found

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
examples\Caviar.sol::2 => pragma solidity ^0.8.17;
examples\LpToken.sol::2 => pragma solidity ^0.8.17;
examples\Pair.sol::2 => pragma solidity ^0.8.17;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)
