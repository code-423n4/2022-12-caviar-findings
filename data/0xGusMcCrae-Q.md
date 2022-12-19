## Finding 1
#### Mismatched input array lengths in pair.wrap() cause panic error
Panic error 0x32 (array accessed at out-of-bounds or negative index) occurs if the proofs array parameter is shorter than the tokenIds array parameter. Error occurs during _validateTokenIds internal function call.

Relevant code: https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L217-L227

Recommend adding a check for matching input array lengths

## Finding 2
#### Calling pair.unwrap with too many items in tokenIds array causes panic error
Panic error 0x11 (arithmetic operation underflow or overflow outside of unchecked block) occurs if the length of tokenIds is greater than the number of fractional tokens in the caller's balance (accounting for token decimals, so 1e18 token balance per array slot). Error occurs when attempting to burn the tokens.

Relevant code: https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L248-L253

Recommend adding a check for adequate balance of fractional tokens to burn

## Finding 3
#### No liquidity ratio enforcement in Pair.add()
Whichever token parameter is not the winner of the 'return Math.min(baseTokenShare, fractionalTokenShare)' call within the addQuote internal function becomes essentially irrelevant. The caller can make that token input arbitrarily large without affecting the result of this function - so they both receive nothing in return and alter the balance of the pool which could have unintended (sometimes quite large) effects on the traded asset's price.

Relevant code: https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L63-L77

Recommend adding some sort of check for correct liqudity ratio in line with the pool's existing reserves, similar to uniswapV2Router02's addLiquidity function: https://github.com/Uniswap/v2-periphery/blob/0335e8f7e1bd1e8d8329fd300aea2ef2f36dd19f/contracts/UniswapV2Router02.sol#L32-L57

#### Example

New pair, no liqudity, the following calls executed sequentially

call Pair.add(1000000,10,0), receive 3162 lp tokens, Pair.price() returns 100000000000000000000000

call Pair.add(10000,10000000000000,0), receive only 31 lp tokens, pair.price() returns 100999999999

call Pair.add(100000000000,10,0), receive 0 lp tokens, pair.price() returns 10000100999979999



## Misc.
The Pair contract should have a way to check which tokenIds are available for unwrapping. Maybe a storage variable containing relevant tokenIds. As of now it's unclear which tokenIds are eligible for a given pair and impossible to tell which ones have already been unwrapped. Unwrap callers essentially have to guess until they find a valid tokenId.

Comments in caviar.sol should be updated to make it clearer that the zero address can be used as the base token parameter to use native ETH as the base token

