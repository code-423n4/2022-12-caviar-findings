## 1  Splitting require() statements that use && saves gas :-

AND opcode consumes 3 gas . So instead of use two require statements

code snippet:-
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71


## 2 Increments/decrements can be unchecked in for-loops:-
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

code snippet-
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39

## 3 Reduce the size of error messages (Long revert Strings)
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

code snippet:-
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L117
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L120
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L80

## 4 Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

code snippet:-
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L19

## 5 Lock Pragma 

code snippet:-
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L2
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L2
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L2