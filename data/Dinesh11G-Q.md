### Unspecific Compiler Version Pragma

#### Impact
Issue Information: 
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2022-12-caviar/src/Caviar.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/LpToken.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/Pair.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/lib/SafeERC20Namer.sol::2 => pragma solidity ^0.8.17;
```
#### Tools used
Manual code review