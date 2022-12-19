# Pair: Split require in order to save gas
Instead of using the ```&&``` operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 3 GAS per ```&&```

```diff
// In Pair.add
-   require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
+   require(baseTokenAmount > 0, ERROR_INPUT_TOKEN_AMOUNT_IS_ZERO);
+   require(fractionalTokenAmount > 0, ERROR_INPUT_TOKEN_AMOUNT_IS_ZERO);
```

# Use _baseTokenReserves instead of baseTokenReserves function inside other functions
Internal functions spend less gass
```solidity
//Pair.sol
399:    return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);
408:    return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);
417:    uint256 baseTokenShare = (baseTokenAmount * lpTokenSupply) / baseTokenReserves();
437:    uint256 baseTokenOutputAmount = (baseTokenReserves() * lpTokenAmount) / lpTokenSupply;
```

# Use balanceOf\[address(this)\] instead of fractionalTokenReserves function inside other functions
```solidity
//Pair.sol
391:    return (_baseTokenReserves() * ONE) / fractionalTokenReserves();
399:    return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);
408:    return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);
422:    uint256 fractionalTokenShare = (fractionalTokenAmount * lpTokenSupply) / fractionalTokenReserves();
438:    uint256 fractionalTokenOutputAmount = (fractionalTokenReserves() * lpTokenAmount) / lpTokenSupply;
```

# Pair#withdraw: Cache closeTimestamp in order to avoid double access to storage variable
```closeTimestamp``` is accessed twice, this can be avoided