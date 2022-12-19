public functions not called by the contract should be declared external instead

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L275-L286
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L294-L304
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L310-L316
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L323-L332
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341-L352
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359-L373
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L390-L392

missing require() on _transFrom

check that the amount > 0
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L447-L459