### Splitting `Require()` Statements that use `&&` saves gas.

**********1 instance**********: [Pair#L71](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L71)

```solidity
require(baseTokenAmount > 0, "Base token amount is zero");
require(fractionalTokenAmount > 0, "Fractional token amount is zero");
```

| Function Name | min  | avg     | median | max     | # calls |
| --- | --- | --- | --- | --- | --- | 
| add (before)      | 485 | 71816 | 84449  | 110061 | 79 |
| add (after)         | 465 | 71808 | 84443  | 110053 | 79 |