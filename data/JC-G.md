
---

## Summary

- G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 2 instances
- G-02  ++i/i++ should be unchecked{++i}/unchecked{i++}  (++j/j++ too) | 7 instances
- G-03 Splitting require() statements that use && saves gas | 1 instance 
- G-04 Empty blocks should be removed or emit something | 1 instance 
- G-05 Functions guaranteed to revert when called by normal users can be marked payable | 2 instances

Total: 13 instances in 5 issues.

---

## G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

Instances (2):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453

lib/SafeERC20Namer.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L35

 
## G-02  ++i/i++ should be unchecked{++i}/unchecked{i++}  (++j/j++ too)

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops. 
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

Instances (7):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468

lib/SafeERC20Namer.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39


## G-03 Splitting require() statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &).

Instance (1):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71
 

## G-04 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. 

Instance (1):

Caviar.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L21


## G-05 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. 
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 
The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

Instances (2):

LpToken.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L26
  
