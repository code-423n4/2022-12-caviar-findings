Potential DoS by division by zero:

In these lines the denominator value should be checked before the division to prevent a possible DoS:
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L399
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L437-L438