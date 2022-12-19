**Issue**

`removeQuote` is called when user tries to remove liquidity from the pair. Calling `remove` on a pool with 0 LP token supply leads to a panic error (division by 0)

Formula in `removeQuote` on [Line 437](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L437) leads to a division by 0 error when called on an empty pool with no LP tokens currently issued

**Recommendation**

Check if supply >0 by adding `assert`. 

```
    function removeQuote(uint256 lpTokenAmount) public view returns (uint256, uint256) {
        uint256 lpTokenSupply = lpToken.totalSupply();
        assert(lpTokenSupply >0);
        uint256 baseTokenOutputAmount = (baseTokenReserves() * lpTokenAmount) / lpTokenSupply;
        uint256 fractionalTokenOutputAmount = (fractionalTokenReserves() * lpTokenAmount) / lpTokenSupply;

        return (baseTokenOutputAmount, fractionalTokenOutputAmount);
    }
```
