# Gas Optimizations Report for Caviar contest
## Overview
During the audit, 2 gas issues were found.  
Total savings ~280.

№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Use unchecked blocks for incrementing i | 7 | 245
G-2 | Use unchecked blocks for subtractions where underflow is impossible | 1 | 35

## Gas Optimizations Findings(2)
### G-1. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L258
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*7 = 245
#

### G-2. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L168

##### Saved
This saves ~35.  