# [G-01] Unused SafeERC20Namer functions

SafeERC20Namer is a library used in Caviar.sol, but SafeERC20Namer contains functions that are never used. Removing these functions saves gas on deployment:
1. bytes32ToString
2. parseStringData

## Recommended Mitigation Steps

Remove [the unused functions](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L10-L44).

# [G-02] Make baseTokenReserves external

`baseTokenReserves` calls the internal `_baseTokenReserves`. All calls to query the base token reserves in this contract should use the internal function while external calls can use the external (currently public) function. This will save gas.

## Recommended Mitigation Steps

Change the [visibility of `baseTokenReserves` to external](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L379) and change internal calls to `_baseTokenReserves`.

# [G-03] Make `price` external

`price` [is declared public](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L390) but is not called internally so it can be external for gas savings.

## Recommended Mitigation Steps

Change the visibility of `price` to external