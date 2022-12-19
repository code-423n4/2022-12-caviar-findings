# Gas Report

## Use `private` rather than `public` for constants
Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table. If needed to be viewed externally, the values can be read from the verified contract source code.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L20
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L21

<br>

## `public` functions not called internally can be marked `external`
Since `public` functions require Solidity to copy arguments to memory, whereas `external` functions simply read from calldata, `external` functions result in lower gas costs.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L280
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L295
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L310
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L324
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359

<br>

## Have contract call `internal` function rather than `public` one
The `public` getter function `baseTokenReserves` simply calls the `internal` function `_baseTokenReserves`. When calling this getter from within the contract, it is 35 gas cheaper to call the `internal` version than the `public` version. Moreover, this allows us to change the visibility of `baseTokenReserves` to `external` to further save gas.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L399
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L408
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L421
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L437

<br>

## Split require statements using `&&`
Replacing with two separate require statements saves 16 gas per instance
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71

<br>

## Use `unchecked` for calculations that cannot overflow
By bypassing Solidity's built in overflow/underflow checks using `unchecked`, we can save gas. This is especially beneficial for the index variable within for loops (saves 120 gas per iteration).
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L168
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238

<br>

## Cache array length when using for loops
Caching the array length outside a loop saves reading it on each iteration, as long as the array’s length is not changed during the loop.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468

<br>

## Functions guaranteed to revert when called by normal users can be marked payable
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. This saves 24 gas per call
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359

<br>

## `x = x + y` is cheaper than `x += y`
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453