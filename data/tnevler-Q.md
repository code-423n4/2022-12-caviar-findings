# Report
## Non-Critical Issues ##
### [N-1]: Wrong order of functions
**Context:**

```function tokenSymbol(address token) internal view returns (string memory) {``` [L76](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L76) (internal function can not go after private function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-2]: NatSpec is missing
**Context:**

1. ```function baseTokenReserves() public view returns (uint256) {``` [L379](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L379) 
1. ```function fractionalTokenReserves() public view returns (uint256) {``` [L383](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L383) 
