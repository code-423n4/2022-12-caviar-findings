# [Q-01] TYPO IN TYPE CASTING

/src/Pair.sol: [L465](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465)

```diff
-   if (merkleRoot == bytes23(0)) return;
+   if (merkleRoot == bytes32(0)) return;
```