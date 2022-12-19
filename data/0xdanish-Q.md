##### solmate/auth/Owned.sol
### Missing zero address checks in transferOwnership()
891:        `owner = newOwner;`
Recommendation:  Add require check for zero address validation

##### src/Pair.sol
### Floating pragma: files should use fixed compiler versions, not floating ones and 0.8.17 is not recommended for deployment.
2:             `pragma solidity ^0.8.17;`

### Calls inside a loop might lead to a denial-of-service attack.
281:         `for (uint256 i = 0; i < tokenIds.length; i++) {`
Recommendation:  Favour pull over push strategy for external calls.

##### src/Caviar.sol
### Floating pragma: files should use fixed compiler versions, not floating ones.
2:             `pragma solidity ^0.8.17;`

##### src/LpToken.sol
### Floating pragma: files should use fixed compiler versions, not floating ones.
2:             `pragma solidity ^0.8.17;`