
## [G-01] redudant return in `_transferFrom()` function

The `transferFrom()` function currently returns a boolean value that is not utilized in the code. Additionally, if the `amount` is greater than the `balanceOf[from]`, the function will cause an arithmetic underflow/overflow and revert. We recommend modifying the function to a non-returning function to reduce gas usage.

```solidity
function _transferFrom(address from, address to, uint256 amount) internal {
	balanceOf[from] -= amount;
	
	// Cannot overflow because the sum of all user
	// balances can't exceed the max uint256 value.
	unchecked {
		balanceOf[to] += amount;
	}
	
	emit Transfer(from, to, amount);
}
```

### Commands used

```bash
forge snapshot --match-contract AddBuySellRemoveTest --gas-report --diff .gas-snapshot-returns-1 -vv
```

```bash
Output:

testItAddsBuysSellsRemovesCorrectAmount(uint256,uint256,uint256) (gas: -44 (-0.006%)) 
Overall gas change: -44 (-0.006%)
```

## [GAS-02] Using `private` rather than `public` for immutable, saves gas

By declaring state variables as private and not allowing them to be accessed outside the contract, gas usage can be optimized

_There are 5 instances of this issue:_

```solidity
File: src/Pair.sol

Line 23: address private immutable nft;
Line 24: address private immutable baseToken;
Line 25: bytes32 private immutable merkleRoot;
Line 26: LpToken public immutable lpToken;
Line 27: Caviar private immutable caviar;

```