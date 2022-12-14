### Few gas optimizations 

1. Functions can be external instead of public
	* [Caviar.sol#28](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28)
	* [Caviar.sol#L49](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L49)
	* [LpToken.sol#L19](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19)
	* [LpToken.sol#26](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L26)

2. Use `!=0` instead of `>0` for unsigned integers
	* [Pair.sol#L71](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71)


