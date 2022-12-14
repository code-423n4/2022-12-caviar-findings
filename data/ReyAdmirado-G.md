| | issue |
| ----------- | ----------- |
| 1 | [state variables should be cached in stack variables rather than re-reading them from storage](#1-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 2 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#2-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 3 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#3-x--y-costs-more-gas-than-x--x--y) |
| 4 | [can make the variable outside the loop to save gas](#4-can-make-the-variable-outside-the-loop-to-save-gas) |
| 5 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#5-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 6 | [splitting require() statements that use `&&` saves gas](#6-splitting-require-statements-that-use--saves-gas) |
| 7 | [internal functions only called once can be inlined to save gas](#7-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 8 | [public functions not called by the contract should be declared external instead](#8-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 9 | [before some functions we should check some variables for possible gas save](#9-before-some-functions-we-should-check-some-variables-for-possible-gas-save) |
| 10 | [pre calculate instead of using operations to calculate](#10-pre-calculate-instead-of-using-operations-to-calculate) |
| 11 | [emit can be made cheaper](#11-emit-can-be-made-cheaper) |

## 1. state variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

high chance of saving 100 gas for a low risk of losing 3, cache `closeTimestamp` before the second require (only if withdraw is not initiated we will lose 3 gas otherwise we will save 100)
- [Pair.sol#L364](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L364)


## 2. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

can not underflow
- [SafeERC20Namer.sol#L55](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L55)

checked in require L157
- [Pair.sol#L168](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L168)


## 3. `<x> += <y>` costs more gas than `<x> = <x> + <y>`
Using the addition operator instead of plus-equals saves gas

- [Pair.sol#L448](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448)
- [Pair.sol#L453](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453)


## 4. can make the variable outside the loop to save gas

make the variable before the for loop and only give the value to it inside the loop

`isValid`
- [Pair.sol#L469](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L469)

`char`
- [SafeERC20Namer.sol#L14](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L14)


## 5. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [Pair.sol#L238](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238)
- [Pair.sol#L258](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258)
- [Pair.sol#L468](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468)

- [SafeERC20Namer.sol#L13](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13)
- [SafeERC20Namer.sol#L22](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22)
- [SafeERC20Namer.sol#L33](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33)
- [SafeERC20Namer.sol#L39](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39)


## 6. splitting require() statements that use `&&` saves gas

this will have a large deployment gas cost but with enough runtime calls the split require version will be 3 gas cheaper

- [Pair.sol#L71](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71)


## 7. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_validateTokenIds`
- [Pair.sol#L463](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L463)


## 8. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

`destroy`
- [Caviar.sol#L49](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L49)

`create`
- [Caviar.sol#L28](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28)

`mint`
- [LpToken.sol#L19](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19)

`burn`
- [LpToken.sol#L26](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L26)

`price`
- [Pair.sol#L390](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L390)

`withdraw`
- [Pair.sol#L359](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359)

`close`
- [Pair.sol#L341](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341)

`nftSell`
- [Pair.sol#L323](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L323)

`nftBuy`
- [Pair.sol#L310](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L310)

`nftRemove`
- [Pair.sol#L294](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L294)

`nftAdd`
- [Pair.sol#L275](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L275)


## 9. before some functions we should check some variables for possible gas save

before transfer we should check for amount being 0 so the function doesnt run when its not gonna do anything. varaiables below have possibility of being 0

`fractionalTokenOutputAmount`
- [Pair.sol#L125](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L125)

`baseTokenOutputAmount` 
- [Pair.sol#L134](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L134)
- [Pair.sol#L137](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L137)

`outputAmount`
- [Pair.sol#L162](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L162)
- [Pair.sol#L200](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L200)
- [Pair.sol#L203](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L203)

`inputAmount`
- [Pair.sol#L172](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L172)
- [Pair.sol#L194](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L194)


## 10. pre calculate instead of using operations to calculate

just pre calculate `160 - 4 * 4` and use it inside the code instead of using 2 operations

- [SafeERC20Namer.sol#L55](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L55)

## 11. emit can be made cheaper

`closeTimestamp` costs 100 gas instead just use the value `block.timestamp + CLOSE_GRACE_PERIOD` given to it some line above or cache the value of this operation before to make it even cheaper

- [Pair.sol#L351](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L351)