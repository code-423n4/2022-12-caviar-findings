## Inconsistent comments and require functions

In [Pair.sol#L79-L80](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L79-L80), [Pair.sol#L116-L117](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L116-L117),  [Pair.sol#L119-L120](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L119-L120) and [Pair.sol#L188-L189](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L188-L189) the comments say `>` while the actual code is `>=`.

In [Pair.sol#L156-L157](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L156-L157) the comments say `<` while the actual code is `<=`.

Recommendation: The comments and actual require() calls should be made consistent according to the specification.

## Floating pragma version

Contracts should be deployed with a locked and up to date compiler version that they have been tested with thoroughly. It will help to ensure that the contracts are not deployed with an outdated Solidity compiler version that is known to have vulnerabilities or can cause unexpected behaviour.

Affected:
[Caviar.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L2)
[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L2)
[LpToken.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L2)
[SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L2)

Recommendation: Lock the pragma version

Reference: https://swcregistry.io/docs/SWC-103 

## Typo in type conversion

The `merkleRoot` state variable i declared as `bytes32` in [Pair.sol#L25](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L25) later in [Pair.sol#465](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L465) a comparision of `merkleRoot` with `bytes23(0)` is made

Recommendation: change the conversion to `bytes32(0)`

## Duplicate condition check in functions

Both `close` ([Pair.sol#L343](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L343)) and `withdraw` ([Pair.sol#L361](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L361)) functions have the same `require()` check repeated. In such case it is usually a good practice to use functions modifiers in order to increase code readability.

Recommendation: consider implementing a `onlyOwner` function modifier that implements the duplicate `require()` logic.

## Lack of zero checks for function arguments

Multiple functions in `Pair.sol` don't check token amount arguments:
- `remove` function does not check if `lpTokenAmount` argument is greater than 0
- `buy` function does not check if `outputAmount` argument is greater than 0
- `sell` function does not check if `inputAmount` argument is greater than 0

Recommendation: add require() statements checking the amount of tokens passed to the function call