Using unchecked. Incrementing i inside unchecked block is safe and consumes lesser gas
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468

Constants should be defined rather than using magic numbers
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L399
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L407
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L408

Splitting require() statements that use && saves gas
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71

Use x=x+y instead of x+=y
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L35
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453