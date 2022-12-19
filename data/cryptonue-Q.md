# [N-01] Caviar and LPToken does't need to inherits Owned contract

The `Caviar` and `LpToken` contract both inherits `Owned` contract from `solmate`. But if we look at them, it doesn't have to.

- `Caviar` contract doesn't really make use any of `onlyOwner` modifier or `transferOwnership()` function. The only `Owned` function being used is in `Pair` contract, calling `caviar.owner()` which we can just create a public immutable `owner` in the `Caviar` contract.
  
- `LpToken` contract use the `onlyOwner` modifier but doesn't really use the `transferOwnership()` since the `LpToken` owner is the `Pair` contract. (and no such function will use the `transferOwnership`). We can simply create an immutable `owner` here
  
  ```solidity
  // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.17;
  
  import "solmate/tokens/ERC20.sol";
  
  contract LpToken is ERC20 {
      address immutable owner;
      constructor(string memory pairSymbol)
          ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
      {
          owner = msg.sender;
      }
  
      function mint(address to, uint256 amount) public onlyOwner {
          _mint(to, amount);
      }
  
      function burn(address from, uint256 amount) public onlyOwner {
          _burn(from, amount);
      }
  }
  ```
  

# [N-02] UNSPECIFIC COMPILER VERSION PRAGMA

```solidity
pragma solidity ^0.8.17;
```

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

# [N-03] Not using named import

All import using plain `import 'file.sol`' instead use named import.

```solidity
Plain Import
File: Pair.sol
01: // SPDX-License-Identifier: MIT
02: pragma solidity ^0.8.17;
03: 
04: import "solmate/tokens/ERC20.sol";
05: import "solmate/tokens/ERC721.sol";
06: import "solmate/utils/MerkleProofLib.sol";
07: import "solmate/utils/SafeTransferLib.sol";
08: import "openzeppelin/utils/math/Math.sol";
09: 
10: import "./LpToken.sol";
11: import "./Caviar.sol";

Named import (example)
2: import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
3: import {EnumerableSet} from "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
```