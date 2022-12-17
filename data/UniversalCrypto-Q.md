1. Contract Inheriting Solmate `Owned.sol` should be overridden to prevent `transferOwnership` from being called to address(0) & consider emitting an event for transparency for users.

`owner` is able to call `close()` in `pair.sol` which allows compromised pairs to be removed. If this access is lost compromised pairs will be allowed to exist.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Caviar.sol#L12
https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/LpToken.sol#L10
https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L341

