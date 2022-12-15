# Non critical
## `bytes23` is used instead of `bytes32` for checking if `merkleRoot` is empty

The if statement still behave as expected but the typo should be removed.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465
```solidity
    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
        // if merkle root is not set then all tokens are valid
        if (merkleRoot == bytes23(0)) return;
```