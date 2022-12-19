##### src/Pair.sol
### SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
71:        `require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");`

### X = X + Y IS MORE EFFICIENT, THAN X += Y
453:            `balanceOf[to] += amount;`

### X = X - Y IS MORE EFFICIENT, THAN X -= Y
448:        `balanceOf[from] -= amount;`