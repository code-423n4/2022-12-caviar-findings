1. Solidity compiler version should be statically declared, hence carat should be removed. 
`pragma solidity ^0.8.17;` should be declared as `pragma solidity 0.8.17;`

2. `require(caviar.owner() == msg.sender, "Close: not owner");` A modifier should be used to enforce function access. This occurs in `Pair.sol` in `withdraw()` and `close()` functions.