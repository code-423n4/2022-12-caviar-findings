### When Incrementing in a for loop uncheck each increment.
- Inside of a for loop the variable being incremented cannot overflow because the loop checks each iteration to see if that variable is less than or equal to an specific amount.
https://gist.github.com/devNamedKiki/d63ee50856c8ff1861d5412fecf8b2fb

There are 3 instances of this issue
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468


 ### x += y cost more gas. You can use x = x + y instead.
- Although it is easier to change the value of a variable by using += or -=. It cost more gas than the written out version x = x + y and x = x - y.

There are 2 instances of this issue:
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453