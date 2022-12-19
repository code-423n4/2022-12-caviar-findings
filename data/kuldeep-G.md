# Gas Optimization 1
Unchecked mathematical operation.

## Explanation
`uint256 refundAmount = maxInputAmount - inputAmount;
  if (refundAmount > 0) msg.sender.safeTransferETH(refundAmount);`

This calculation for [`refundAmount`](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L168) can be unchecked as it is already established that [`require(inputAmount <= maxInputAmount, "Slippage: amount in");`](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L157)

## Mitigation
` uint256 refundAmount; unchecked { refundAmount = maxInputAmount - inputAmount; } msg.sender.safeTransferETH(refundAmount);`