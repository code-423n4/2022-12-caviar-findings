### Unused return value of `_transferFrom` function

The internal [`_transferFrom` function](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L447) of the `Pair` contract [returns `true` on success](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L458). However, this value is never used when `_transferFrom` is called in lines [85](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L85), [125](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L125), [162](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L162) and [194](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L194).

These unused values may cause confusion in readers and contributors alike, so I recommend removing it to improve the code's quality. Alternatively, if the `_transferFrom` function is to be left unmodified, the code could include inline comments in the calls to `_transferFrom` to explicitly state why the return value is not used.

### Erroneous casting

The [`_validateTokenIds` function](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465) of the `Pair` contract returns early when the `merkleRoot` state variable is zero. The `if` clause that implements this logic contains an erroneous casting operation when comparing `merkleRoot`. In particular, the comparison is made against a `bytes23` instead of `bytes32`. While this does not cause a problem, to favor correctness it's best to change the casting operation to a `bytes32` type.

### Potential out-of-bounds access in token ID validation

In the [`_validateTokenIds` function](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L463) of the `Pair` contract, there's a `for` loop that accesses the `proofs` and `tokenIds` parameters with the same index. The function implicitly assumes the arrays have the same length, yet it doesn't explicitly verify it. If the `proofs` array is shorter than the `tokenIds` array, execution will unexpectedly revert due to an out-of-bounds access.

It's best to always make function requirements explicit, so as to fail early and loudly. Include a `require` statement to ensure the two arrays have the same length.