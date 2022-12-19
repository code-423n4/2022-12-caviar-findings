https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L16
Recommend using different contracts for maintaining the fractional ownership ERC20 token and implementing the Pair AMM, to limit Pair's permission with regard to managing (eg arbitrarily minting) the ERC20 fractional tokens.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L389
Regarding this comment, if the baseToken uses a different decimals value than the fractional token this may not be "The price of one fractional token in base tokens * 1e18"

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L435-L441
Ensure lpTokenAmount is <= lpTokenSupply and can actually be withdrawn.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L30
This function is unused.