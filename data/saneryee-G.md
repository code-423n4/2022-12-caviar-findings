# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | Splitting require() statements that use `&&` saves gas                                                                             |     1     |
| [G-002] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |    10     |
| [G-003] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |     2     |
| [G-004] | internal functions only called once can be inlined to save gas                                                                     |     1     |

## [G-001] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:1

[src/Pair.sol#L71](https://github.com/code-423n4/2022-12-caviar/tree/main//src/Pair.sol#L71)

```solidity
71:    require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

## [G-002] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:10

[src/Caviar.sol#L36](https://github.com/code-423n4/2022-12-caviar/tree/main//src/Caviar.sol#L36)

```solidity
36:    string memory pairSymbol = string.concat(nftSymbol, ":", baseTokenSymbol);
```

[src/Caviar.sol#L35](https://github.com/code-423n4/2022-12-caviar/tree/main//src/Caviar.sol#L35)

```solidity
35:    string memory nftName = nft.tokenName();
```

[src/Caviar.sol#L34](https://github.com/code-423n4/2022-12-caviar/tree/main//src/Caviar.sol#L34)

```solidity
34:    string memory nftSymbol = nft.tokenSymbol();
```

[src/Caviar.sol#L33](https://github.com/code-423n4/2022-12-caviar/tree/main//src/Caviar.sol#L33)

```solidity
33:    string memory baseTokenSymbol = baseToken == address(0) ? "ETH" : baseToken.tokenSymbol();
```

[src/lib/SafeERC20Namer.sol#L21](https://github.com/code-423n4/2022-12-caviar/tree/main//src/lib/SafeERC20Namer.sol#L21)

```solidity
21:    bytes memory bytesStringTrimmed = new bytes(charCount);
```

[src/lib/SafeERC20Namer.sol#L38](https://github.com/code-423n4/2022-12-caviar/tree/main//src/lib/SafeERC20Namer.sol#L38)

```solidity
38:    bytes memory bytesStringTrimmed = new bytes(charCount);
```

[src/lib/SafeERC20Namer.sol#L11](https://github.com/code-423n4/2022-12-caviar/tree/main//src/lib/SafeERC20Namer.sol#L11)

```solidity
11:    bytes memory bytesString = new bytes(32);
```

[src/lib/SafeERC20Namer.sol#L60](https://github.com/code-423n4/2022-12-caviar/tree/main//src/lib/SafeERC20Namer.sol#L60)

```solidity
60:    (bool success, bytes memory data) = token.staticcall(abi.encodeWithSelector(selector));
```

[src/lib/SafeERC20Namer.sol#L78](https://github.com/code-423n4/2022-12-caviar/tree/main//src/lib/SafeERC20Namer.sol#L78)

```solidity
78:    string memory symbol = callAndParseStringReturn(token, 0x95d89b41);
```

[src/lib/SafeERC20Namer.sol#L89](https://github.com/code-423n4/2022-12-caviar/tree/main//src/lib/SafeERC20Namer.sol#L89)

```solidity
89:    string memory name = callAndParseStringReturn(token, 0x06fdde03);
```

## [G-003] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:2

[src/LpToken.sol#L26](https://github.com/code-423n4/2022-12-caviar/tree/main//src/LpToken.sol#L26)

```solidity
26:    function burn(address from, uint256 amount) public onlyOwner {
```

[src/LpToken.sol#L19](https://github.com/code-423n4/2022-12-caviar/tree/main//src/LpToken.sol#L19)

```solidity
19:    function mint(address to, uint256 amount) public onlyOwner {
```


## [G-004] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:1

[src/Pair.sol#L463](https://github.com/code-423n4/2022-12-caviar/tree/main//src/Pair.sol#L463)

```solidity
463:    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
```

