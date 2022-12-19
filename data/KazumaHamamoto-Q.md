# [HK L-1] Using implicit returns

## Vulnerability Detail
Most functions of pair.sol do not return their return values explicitly. For example, ```add()```,```remove()```,```buy()```,```sell()```,```wrap()```,```unwrap()```,```nftAdd()```,```nftRemove()```,```nftBuy()```,```nftSell()```. These functions do not use explicit return statements.

This will cause regressions that may occur when code changes over time.

## Code Snippet
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L63
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L107
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L147
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L182
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L217
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L248
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L275
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L310
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L323


## Tool used
Manual Review

## Recommendation
Consider using explicit returns instead of implicit returns on all functions with a return value.