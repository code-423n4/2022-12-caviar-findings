`Pair.sol` has 3 functions which expose reentrancy vulnerability: `remove`, `buy` and `sell`.
All of these functions expose reentrancy when calling `msg.sender.safeTransferETH`. This can be exploited by calling any of the functions from a malicious contract and re-entering any of the `Pair.sol` contract's functions from the `receive` function in the contract.
This can be fixed by adding the `nonReentrant` modifier from Openzeppelin's `ReentrancyGuard` contract to all of the functions in the `Pair.sol` contract.

Use prettier to format code - looks much better and is easier to read.

