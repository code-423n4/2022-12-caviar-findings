# Summary

The Caviar codebase is very well written and follows a clean, permisionless model.

## Low Risk Findings

| ID  | Finding | Instances |
| --- | ------- | --------- |
| L-01    | Anybody can create a pair without funding        |     N/A      |

## Non-critical Findings

| ID  | Finding | Instances |
| --- | ------- | --------- |
| N-01    |  Use WETH instead of native ETH       | N/A          |

# Low Risk Findings

## \[L-01\] Anybody can create a pair without funding

`Caviar.create()` is not permissioned, and there is no cost other than a gas cost for invoking the operation.

As a result, it would be possible to pollute the `Caviar` contract with unfunded pairs.

This is a low-risk issue because front-ends could easily choose to filter out inactive or unfunded pairs.


# Non-critical Findings

## \[N-01\] Use WETH instead of native ETH

In the `Pair` contract there are various branches of logic to handle native ETH vs. ERC20 tokens. It's not clear why native ETH support is necessary, since WETH is available.

According to the authors own [specification](https://github.com/code-423n4/2022-12-caviar/blob/main/docs/SPECIFICATION.md), Caviar is based on Uniswap V2. One of the big changes from Uniswap V1 to Uniswap V2 was to move from native ETH to WETH. While Uniswap offers much more functionality than Caviar, one of the benefits that still applies to Caviar is a simpler codebase:  having extra branches to handle ETH increases the contract complexity, and therefore the risk of defects.