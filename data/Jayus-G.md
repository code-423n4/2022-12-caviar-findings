1. Looping over arrays could be made more efficient by storing the length of the array prior to looping  rather than having the EVM lookup the array length on each iteration. 
2. Also the iterator/counter `i` can be incremented in unchecked block.

Eg. ``uint256 tokenIdsLength = tokenIds.length;
        for (uint256 i = 0; i < tokenIdsLength;) {
            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
            unchecked{
                i++;
            }
        }``

Prior to optimizations in `Pair.sol`.
 `Deployment costs: 3342647`
`Deployment size: 19392`

After optimizations:
`Deployment costs: 3342239`
`Deployment size: 19390`