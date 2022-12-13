## It costs less gas to use ++i compared to i++
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L17
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39

Use `++i` instead of `i++`, especially in loops.


## Functions not called by the contract should be declared external instead
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L26
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L49
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L218
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L280
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L294
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L310
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L324
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L390

Change function accessibility from `public` to `external`.