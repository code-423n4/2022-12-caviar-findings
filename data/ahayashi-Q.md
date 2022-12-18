## Title
[NC-AH-01] Use `byte32(0)` instead of `byte23(0)`

## Summary
`merkleRoot` is defined as `bytes32` type but it is compared to `bytes23(0)`.

This is a different issue from the one noted in [GAS-2](https://gist.github.com/Picodes/42f9144fd8cba738f3a7098411737760#GAS-2) though it could be resolved by reflecting the GAS-2 remarks, but may not do so for readability.

## Lines
```solidity
File: src/Pair.sol

25:  bytes32 public immutable merkleRoot;

465:  if (merkleRoot == bytes23(0)) return;
```
