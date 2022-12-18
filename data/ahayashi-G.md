## Title
[G-AH-01] Use x = x + y instead of x += y

## Summary
The same for the `-=` operator.

## Lines
```solidity
File: src/Pair.sol
448:  balanceOf[from] -= amount;

453:  balanceOf[to] += amount;
```
