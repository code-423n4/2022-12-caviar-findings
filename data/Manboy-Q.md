It seems like the Pari contract has two distinct responsibilities:

1. Being an AMM
- holding the reserves and allowing for trading
- minting/burning LP tokens

2. Being the fractional token:
- wrapping/unwrapping and holding NFTs
- minting/burning and managing (being the ERC20) the fractional tokens

In the spirit of the single-responsibility principle, I recommend extracting the fractional token (2) functionality into a separate contract (FractionalToken.sol).

It's good practice to keep the token contract as simple as possible so that it can easily be reasoned about, especially because users might use the fractional tokens in protocols other than the native AMM.
This will also make it more gas efficient to interact with the fractional token (in most situations) since the number of functions in the contract is reduced.