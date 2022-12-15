Gas optimization issue in the for loop of lines:
https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L238-L240
The increment of i should be marked as unchecked instead of incrementing it the way it is done. Just left a snippet below:

Solution to https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L238-L240:

BEFORE:
for (uint256 i = 0; i < tokenIds.length; i++) {
            ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
        }

AFTER:
for (uint256 i = 0; i < tokenIds.length; ) {
    ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
    unchecked {   ++ i ; }        
}