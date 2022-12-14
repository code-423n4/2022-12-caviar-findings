
QA: Use the interface to send transactions to an external contract (caviar from pool) instead of importing the contract.

NC: Presumably merkleRoot should be compared to bytes32 instead of bytes23 variable to avoid unnecessary conversions. This was probably a typo.
https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L