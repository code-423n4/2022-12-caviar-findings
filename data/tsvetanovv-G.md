## [G-01] SPLITTING REQUIRE() STATEMENTS THAT USE `&&` SAVES GAS

[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71)

```
require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

## [G-02] UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW

[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238-L240)

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.
[Reference](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

```
238:        for (uint256 i = 0; i < tokenIds.length; i++) {
239:            ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
240:        }

258:        for (uint256 i = 0; i < tokenIds.length; i++) {
259:            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
260:        }

468:        for (uint256 i = 0; i < tokenIds.length; i++) {
469:            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
470:           require(isValid, "Invalid merkle proof");
471:            }
```

And all cases in [SafeERC20Namer](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol)

## [G-03] `x = x - y` costs less gas than `x -= y`, same for addition

[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L447)

```
448:        balanceOf[from] -= amount;
453:        balanceOf[to] += amount;
```

And all cases in [SafeERC20Namer](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol)

You can replace all `-=` and `+=` occurrences to save gas
