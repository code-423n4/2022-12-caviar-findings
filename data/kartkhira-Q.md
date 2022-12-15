Code with Issue : https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L407

Here we have hard coded fees as 30bps. This is not ideal as this does not give the flexibility of increasing/decreasing this fees. Owner(Pool creator) might want to decrease the fees to make it more lucrative for traders to trade in his pool. Or he might want to increase fees given the rarity of his NFTs.

I would recommend a immutable fees variables set by the pool owner at the time of creating pool. This way owner will have the flexibility of controlling his pool and the fees also cannot be changed later giving assurance to traders of his pool.