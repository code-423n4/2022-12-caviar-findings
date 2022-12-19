Gas optimization:
Use transferFrom not safeTransferFrom to avoid an unnecessary contract call when transferring ERC721s
`pairs[nf][baseToken][merkleRoot]` does 3 hashes, if the hash was pairHash = `keccak256(abi.encode(nft, baseToken, merkleRoot))`, `pairs[pairHash]`, there would only be one hash which is more gas efficient.
tokenName and tokenSymbol can be dynamically composed based off the base NFT rather than stored as in the current implementation to reduce deployment cost
Constants can be declared immutable to directly replace in the code to lower contract size
