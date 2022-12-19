## Use msg.value in place of maxInputAmount

Since they are checked to be equal, use msg.value in place of maxInputAmount

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L168

```solidity
            uint256 refundAmount = maxInputAmount - inputAmount;
```

## Unnecessary safeTransferFrom to address(this)

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L239

```solidity
            ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
```