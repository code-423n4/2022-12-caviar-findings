## [G-01] Redundant return value.

The return value in `_transferFrom` function is redundant and is not used anywhere, recommended to remove the return statement.

```solidity
function _transferFrom(address from, address to, uint256 amount) internal returns (bool) {
        balanceOf[from] -= amount;

        // Cannot overflow because the sum of all user
        // balances can't exceed the max uint256 value.
        unchecked {
            balanceOf[to] += amount;
        }

        emit Transfer(from, to, amount);

        return true;
    }
```

Calls to `_transferFrom` don’t use the return value:

```solidity
./Pair.sol:85:        _transferFrom(msg.sender, address(this), fractionalTokenAmount);
./Pair.sol:125:        _transferFrom(address(this), msg.sender, fractionalTokenOutputAmount);
./Pair.sol:163:        _transferFrom(address(this), msg.sender, outputAmount);
./Pair.sol:195:        _transferFrom(msg.sender, address(this), inputAmount);
```