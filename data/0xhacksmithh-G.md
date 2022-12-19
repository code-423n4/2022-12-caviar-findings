
### [Gas-01] MULTIPLE ADDRESS MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS TO A STRUCT, WHERE APPROPRIATE

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

*Instances(1)*

```solidity
File:    Caviar.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L19
```

### [Gas-02] INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible

*Instances(9)*

```solidity
File:    Pair.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468
```
```solidity
File:   lib/SafeERC20Namer.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L17
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L35
https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39
```

### [Gas-03] Using private rather than public for some state variable, can saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances(5)*

```solidity
File:    Pair.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L23-L27
```

### [Gas-04] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

*Instances(2)*

```solidity
File:    Pair.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453
```

### [Gas-05] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

See this issue which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas.

*Instances(1)*

```solidity
File:    Pair.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71
```