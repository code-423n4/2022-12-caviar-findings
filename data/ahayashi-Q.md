# [NC-AH-01] Use `byte32(0)` instead of `byte23(0)`

## Summary
`merkleRoot` is defined as `bytes32` type but it is compared to `bytes23(0)`.

This is a different issue from the one noted in [GAS-2](https://gist.github.com/Picodes/42f9144fd8cba738f3a7098411737760#GAS-2) though it could be resolved by reflecting the GAS-2 remarks, but may not do so for readability.

## Lines
```solidity
File: src/Pair.sol

25:  bytes32 public immutable merkleRoot;

465:  if (merkleRoot == bytes23(0)) return;
```

# [NC-AH-02] Use a clearer constant name

## Summary
The use of `ONE` as a constant name for the amount of fractional tokens per NFT is confusing, so it should be named clearer like `FRACTIONAL_TOKEN_AMOUNT_PER_NFT`.

## Lines
```solidity
File: src/Pair.sol

20:  uint256 public constant ONE = 1e18;
```

# [NC-AH-03] Add indexed fields to event

## Summary
A parameter is stored as topic by adding `indexed` to it and off-chain tools can quickly analyze it.


## Lines
```solidity
File: src/Pair.sol

30:    event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
31:    event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
32:    event Buy(uint256 inputAmount, uint256 outputAmount);
33:    event Sell(uint256 inputAmount, uint256 outputAmount);
34:    event Wrap(uint256[] tokenIds);
35:    event Unwrap(uint256[] tokenIds);
36:    event Close(uint256 closeTimestamp);
37:    event Withdraw(uint256 tokenId);
```

```solidity
File: src/Caviar.sol

30:    event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
31:    event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
32:    event Buy(uint256 inputAmount, uint256 outputAmount);
33:    event Sell(uint256 inputAmount, uint256 outputAmount);
34:    event Wrap(uint256[] tokenIds);
35:    event Unwrap(uint256[] tokenIds);
36:    event Close(uint256 closeTimestamp);
37:    event Withdraw(uint256 tokenId);
```


# [NC-AH-04] `wrap`, `unwrap`, `nftBuy` and `nftSell` can be called with a empty `tokenIds`

## Summary
They can also be called from the demo app.

# [NC-AH-05] zero amount of base token can be transferred

## Summary
`buy` method can be called with `buy(0, 0)` and zero amount transfer is executed at L172.

Same goes with `sell` method.

## Lines
```solidity
File: src/Pair.sol

172:  ERC20(baseToken).safeTransferFrom(msg.sender, address(this), inputAmount);
```

