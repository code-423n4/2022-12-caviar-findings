On the Pair.sol contract, line 169, there is no indentation within the if statement, which is good practice, especially when this was a for loop.

Currently:     if (refundAmount > 0) msg.sender.safeTransferETH(refundAmount);

Fixed:     if (refundAmount > 0) 
                       msg.sender.safeTransferETH(refundAmount);