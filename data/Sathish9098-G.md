
##

## [GAS-1]    instead of using operator && on single require checks . using double REQUIRE checks can save more gas. 
After splitting require statement its possible to save 8 gas

>  BEFORE MIGRATION : 

require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

As per remix execution cost  :  21262


>  Recommended Mitigation Steps

require(baseTokenAmount > 0, "Input token amount is zero");
require(fractionalTokenAmount > 0, "Input token amount is zero");

After Mitigation the execution cost :  21254

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol)

    71:  require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");






















GAS-1	Use selfbalance() instead of address(this).balance	1
GAS-2	Use assembly to check for address(0)	10
GAS-3	Cache array length outside of loop	3
GAS-4	Use calldata instead of memory for function arguments that do not get mutated	4
GAS-5	Use Custom Errors	16
GAS-6	Don't initialize variables with default value	8
GAS-7	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	8
GAS-8	Using private rather than public for constants, saves gas	2
GAS-9	Use != 0 instead of > 0 for unsigned integer comparison	4
GAS-10	internal functions not called by the contract should be removed	2