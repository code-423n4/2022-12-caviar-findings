## L01 - Array input length are not checked to be the same

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L217

`wrap()` takes in `tokenIds[]` and `proofs[][]` but their lengths are not checked to be the same. It is recommended that we check length to be equal to avoid unintended actions by the users. 

## L02 - Solmate's safeTransferFrom does not check for code existence

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L239

In the event that NFT contract gets temporarily destroyed, users can mint `fractionalToken` for free since the `safeTransferFrom` call would always succeed even if users to do not own the required NFTs.

## Q01 - Use the special ETH burn address to represent when baseToken is ETH

Instead of setting baseToken to address(0) to represent baseToken is ETH, we can consider using the vanity address 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE to represent this. This makes it much clearer that we are using ETH as the baseToken.