1. https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465
 smol typo: bytes23 -> bytes32
2. https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L390-L392
price function can be manipulated, and there is no other way to get price. Consider adding moving average price function.
3. https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L90
Can mint 0 lp tokens. Consider adding constrait that lpToken amount should be above 0