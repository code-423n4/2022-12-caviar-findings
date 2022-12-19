For Pair.sol, line 275 to 279. I would suggest this order of variable caching to ensure less memory overload for the nftAdd() function:

        uint256 baseTokenAmount,
        uint256 minLpTokenAmount,
        uint256[] calldata tokenIds,
        bytes32[][] calldata proofs