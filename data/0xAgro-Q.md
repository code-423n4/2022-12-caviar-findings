# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Order of Functions Not Compliant With Solidity Docs|1|
|[NC-02]|Contract Not Using NatSpec|1|
|[L-01]|Comment Does Not Match Instruction|5|

### [NC-01] Order of Functions Not Compliant With Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) suggests the following function ordering:  constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

#### Findings:

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

[SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol): internal functions are positioned after private functions.

### [NC-02] Contract Not Using NatSpec

#### Findings:

The [SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol) contract does not use [NatSpec](https://docs.soliditylang.org/en/develop/natspec-format.html) headers per function unlike the other contracts in the codebase. It is best practice to keep a consistant style. Consider using [NatSpec](https://docs.soliditylang.org/en/develop/natspec-format.html) throughout all contracts.

### [L-01] Comment Does Not Match Instruction

#### Findings:

[L80](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L80) checks if the amount of lp tokens is greater OR EQUAL TO the min amount. The comment on [L79](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L79) does not match stating: *"check that the amount of lp tokens outputted is greater than the min amount"*.

[L117](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L117) checks if the base token amount is greater OR EQUAL TO the min amount. The comment on [L116](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L116) does not match stating: *"check that the base token output amount is greater than the min amount"*.

[L120](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L120) checks if the fractional token output is greater OR EQUAL TO the min amount. The comment on [L119](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L119) does not match stating: *"check that the fractional token output amount is greater than the min amount"*.

[L157](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L157) checks if the amout of base tokens is less OR EQUAL TO the max amount. The comment on [L156](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L156) does not match stating: *"check that the required amount of base tokens is less than the max amount"*.

[L189](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L189) checks if the amout of fractional tokens is greater OR EQUAL TO the min amount. The comment on [L188](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L188) does not match stating: *"check that the outputted amount of fractional tokens is greater than the min amount"*.

*/src/Pair.sol*
Links: [79-80](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L79-L80), [116-117](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L116-L117), [119-120](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L119-L120), [156-157](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L156-L157), [188-189](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L188-L189).
```solidity
79:	// check that the amount of lp tokens outputted is greater than the min amount
80:	require(lpTokenAmount >= minLpTokenAmount, "Slippage: lp token amount out");
```
```solidity
116:	// check that the base token output amount is greater than the min amount
117:	require(baseTokenOutputAmount >= minBaseTokenOutputAmount, "Slippage: base token amount out");
```
```solidity
119:	// check that the fractional token output amount is greater than the min amount
120:	require(fractionalTokenOutputAmount >= minFractionalTokenOutputAmount, "Slippage: fractional token out");
```
```solidity
156:	// check that the required amount of base tokens is less than the max amount
157:	require(inputAmount <= maxInputAmount, "Slippage: amount in");
```
```solidity
188:	check that the outputted amount of fractional tokens is greater than the min amount
189:	require(outputAmount >= minOutputAmount, "Slippage: amount out");
```