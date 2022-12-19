# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1    | Use `unchecked` blocks to save gas  |  1 |
| 2    | internal function `_baseTokenReserves()` should be called to get the base token reserve instead of `baseTokenReserves()`  |  4 |
| 3    | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  2 |
| 4    | Splitting `require()` statements that uses && saves gas  |  1 |
| 5    | `public` functions not called by the contract should be declared `external` instead |  7 |
| 6    | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  7  |


## Findings

### 1- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There is 1 instance of this issue:

File: src/Pair.sol [Line 157](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L157)
```
uint256 refundAmount = maxInputAmount - inputAmount;
```

The above operation cannot underflow because of the check : 

File: src/Pair.sol [Line 168](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L168)
```
require(inputAmount <= maxInputAmount, "Slippage: amount in");
```

So the operation should be marked unchecked.

### 2- internal function `_baseTokenReserves()` should be called to get the base token reserve instead of `baseTokenReserves()`  :

When a function inside the Pair contract need to get the amount of base token reserve it should call the internal function `_baseTokenReserves()` instead of the public one `baseTokenReserves()`, this will save around **30-40 gas** (on average) for each function call.

Additionaly the `baseTokenReserves()` function could be marked `external` to save further gas.

There are 4 instances of this issue :

File: src/Pair.sol [Line 399](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L399)
```
return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);
```

File: src/Pair.sol [Line 408](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L408)
```
return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);
```

File: src/Pair.sol [Line 421](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L421)
```
uint256 baseTokenShare = (baseTokenAmount * lpTokenSupply) / baseTokenReserves();
```

File: src/Pair.sol [Line 437](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L437)
```
uint256 baseTokenOutputAmount = (baseTokenReserves() * lpTokenAmount) / lpTokenSupply;
```

### 3- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 2 instances of this issue:

File: src/Pair.sol [Line 448](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448)
```
balanceOf[from] -= amount;
```

File: src/Pair.sol [Line 453](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453)
```
balanceOf[to] += amount;
```

### 4- Splitting `require()` statements that uses && saves gas :

There is 1 instance of this issue :

File: src/Pair.sol [Line 71](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71)

```
require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

### 5- `public` functions not called by the contract should be declared `external` instead :

The `public` functions that are not called inside the contract should be declared `external` instead to save gas.

There are 7 instances of this issue:

File: src/Caviar.sol [Line 28](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28)
```
function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair)
```

File: src/Caviar.sol [Line 49](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L49)
```
function destroy(address nft, address baseToken, bytes32 merkleRoot) public
```

File: src/LpToken.sol [Line 19](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19)
```
function mint(address to, uint256 amount) public 
```

File: src/LpToken.sol [Line 26](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L26)
```
function mint(address to, uint256 amount) public 
```

File: src/Pair.sol [Line 341](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341)
```
function close() public
```

File: src/Pair.sol [Line 359](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359)
```
function withdraw(uint256 tokenId) public
```

File: src/Pair.sol [Line 390](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L390)
```
function price() public view returns (uint256)
```

### 6- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 7 instances of this issue:
 
```
File: src/Pair.sol

238     for (uint256 i = 0; i < tokenIds.length; i++)
258     for (uint256 i = 0; i < tokenIds.length; i++)
468     for (uint256 i = 0; i < tokenIds.length; i++)

File: contracts/Lock.sol

12      for (uint256 j = 0; j < 32; j++)
22      for (uint256 j = 0; j < charCount; j++)
33      for (uint256 i = 32; i < 64; i++)
39      for (uint256 i = 0; i < charCount; i++)
```