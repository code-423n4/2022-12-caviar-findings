# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | x += y costs more gas than x = x + y for state variables | 3 |
| [GAS-2](#GAS-2) | Splitting require() statements that use && saves gas | 1 |
| [GAS-3](#GAS-3) | Add Unchecked {} For Subtractions Where The Operands Cannot Underflow Because Of A Previous Require() Or If-statement | 1 |
| [GAS-4](#GAS-4) | Functions Guaranteed To Revert When Called By Normal Users Can Be Marked Payable | 2 |

###  [GAS-1] x += y costs more gas than x = x + y for state variables
use = + or = - instead to save gas

*Instances (3)*:
```solidity
File: src/Pair.sol

448:             balanceOf[from] -= amount;

453:             balanceOf[to] += amount;

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)

```solidity
File: src/lib/SafeERC20Namer.sol

35:             charCount += uint8(b[i]);

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib/SafeERC20Namer.sol)

###  [GAS-2] Splitting require() statements that use && saves gas
Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas.

*Instances (1)*:
```solidity
File: src/Pair.sol

71:             require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)


###  [GAS-3] Add Unchecked {} For Subtractions Where The Operands Cannot Underflow Because Of A Previous Require() Or If-statement
The unchecked keyword can saves 30-40 gas per line.

*Instances (1)*:
```solidity
File: src/Pair.sol

168:             uint256 refundAmount = maxInputAmount - inputAmount;

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol)


###  [GAS-4] Functions Guaranteed To Revert When Called By Normal Users Can Be Marked Payable
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (2)*:
```solidity
File: src/LpToken.sol

19:             function mint(address to, uint256 amount) public onlyOwner {

26:             function burn(address from, uint256 amount) public onlyOwner {

```
[Link to code](https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol)


