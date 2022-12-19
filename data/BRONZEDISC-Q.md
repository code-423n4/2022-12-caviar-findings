## QA

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.


https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol

```solidity
// events coming before state variables
15:    event Create(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
16:    event Destroy(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```solidity
// these internal functions should come before all the private ones
76:    function tokenSymbol(address token) internal view returns (string memory) {
87:    function tokenName(address token) internal view returns (string memory) {
```

---

### natSpec missing [3]

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```solidity
10:    function bytes32ToString(bytes32 x) private pure returns (string memory) {

// missing @param and @return
30:    function parseStringData(bytes memory b) private pure returns (string memory) {
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```solidity
30:    event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
31:    event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
32:    event Buy(uint256 inputAmount, uint256 outputAmount);
33:    event Sell(uint256 inputAmount, uint256 outputAmount);
34:    event Wrap(uint256[] tokenIds);
35:    event Unwrap(uint256[] tokenIds);
36:    event Close(uint256 closeTimestamp);
37:    event Withdraw(uint256 tokenId);

39:    constructor(

379:    function baseTokenReserves() public view returns (uint256) {

383:    function fractionalTokenReserves() public view returns (uint256) {

447:    function _transferFrom(address from, address to, uint256 amount) internal returns (bool) {

// @param missing
463:    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {

```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol

```solidity
11:    constructor(string memory pairSymbol)
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
  - private and internal `variables` should preppend with `underline`
  - private and internal `functions` should preppend with `underline`
  - constant state variables should be UPPER_CASE

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```solidity
10:    function bytes32ToString(bytes32 x) private pure returns (string memory) {

30:    function parseStringData(bytes memory b) private pure returns (string memory) {

48:    function addressToName(address token) private pure returns (string memory) {

54:    function addressToSymbol(address token) private pure returns (string memory) {

59:    function callAndParseStringReturn(address token, bytes4 selector) private view returns (string memory) {

76:    function tokenSymbol(address token) internal view returns (string memory) {

87:    function tokenName(address token) internal view returns (string memory) {
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```solidity
2:  pragma solidity ^0.8.17;
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```solidity
2:  pragma solidity ^0.8.17;
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol

```solidity
2:  pragma solidity ^0.8.17;
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol

```solidity
2:  pragma solidity ^0.8.17;
```
