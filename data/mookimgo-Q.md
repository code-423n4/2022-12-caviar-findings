https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465

```
if (merkleRoot == bytes23(0)) return;
```

wierd bytes23, may be a typo of bytes32? as the merkleRoot is bytes32