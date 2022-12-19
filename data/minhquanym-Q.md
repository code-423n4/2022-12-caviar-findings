# Summary

| Id | Title |
| -- | ----- |
| 1 | High price NFT will be unwrap immediately, replaced by a lower value NFT |
| 2 | Unwrap did not check whether tokenId is in merkle proof |

# 1. High price NFT will be unwrap immediately, replaced by a lower value NFT

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L248

## Detail
It is evident that with `wrap()/unwrap()`, an individual can exchange a lower-valued Non-Fungible Token (NFT) for a higher-valued one within the same collection. This is because each NFT is unique, and thus, usually has its own distinct value/price.

The result of this design will be that only one (or a few) NFTs with the lowest value will remain in the Pair. This is because if someone wraps an NFT which has a price slightly higher than the floor price, other traders will immediately trade it for one at the floor price.


# 2. Unwrap did not check whether tokenId is in merkle proof.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L248

## Detail
If an NFT is mistakenly directly sent to the Pair contract not included in merkle proof, the FracToken can be used to withdraw it with `unwrap()` function.

Consider the scenario where this NFT has higher value than all NFTs in merkle proof, it should be withdrawn by admin to possibly return to true owner.
