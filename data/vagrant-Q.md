Caviar.sol:
create() can be marked external since it is never called from within the contract

nft and baseTokens existence are not checked

Pair.sol:
wrap() does not check whether array lengths are the same

nftAdd(), nftRemove(), nftBuy(), nftSell(), close(), withdraw() can be marked external, since they are never called from within the contract

