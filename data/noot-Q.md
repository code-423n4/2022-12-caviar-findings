# Multiple pairs can have the same pairSymbol



Inside Caviar.create(), the pairSymbol is created by concatenating the nftSymbol and baseTokenSymbol:
```
string memory pairSymbol = string.concat(nftSymbol, ":", baseTokenSymbol);
pair = new Pair(nft, baseToken, merkleRoot, pairSymbol, nftName, nftSymbol);
```

However, there's no check for if a pair already has that pairSymbol. The addresses of the tokens and pair contract cannot be duplicates, but anyone is able to create new pairs that may have the same pairSymbol as another existing one, causing confusion.

Recommendation: hash and store pairSymbol in a map and check if it already exists.


# Division by zero if trying to buy all fractional tokens held by Pair.sol

If a user inputs `outputAmount == fractionalTokenReserve()` in `Pair.buy()`, there is a division by zero exception in `buyQuote`. This is fine logically, but a check for a zero denominator that has an error message such as "cannot buy all fractional token reserves" would potentially be more user-friendly.