# QA Report for Caviar contest
## Overview
During the audit, 3 non-critical issues were found.
№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | Order of Functions | Non-Critical | 2
NC-2 | Order of Layout | Non-Critical | 1
NC-3 | Missing NatSpec | Non-Critical | 2

## Non-Critical Risk Findings(3)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
Internal functions should be placed before private:
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L76
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L87

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
State variables should be placed before events:
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L19

##### Recommendation
Place events after state variables.
#
### NC-3. Missing NatSpec
##### Description
NatSpec is missing for 2 functions in 1 contract.
##### Instances
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L379 
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L383

##### Recommendation
Add NatSpec for all functions.