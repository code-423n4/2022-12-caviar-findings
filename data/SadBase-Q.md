## [N-01] Missing NatSpec in functions

```solidity
File: src/Pair.sol

379: function baseTokenReserves() public view returns (uint256) {

383: function fractionalTokenReserves() public view returns (uint256) {

447: function _transferFrom(address from, address to, uint256 amount) internal returns (bool) {
```

```solidity
File: src/lib/SafeERC20Namer.sol

10: function bytes32ToString(bytes32 x) private pure returns (string memory) {

48: function addressToName(address token) private pure returns (string memory) {

54: function addressToSymbol(address token) private pure returns (string memory) {

59: function callAndParseStringReturn(address token, bytes4 selector) private view returns (string memory) {

76: function tokenSymbol(address token) internal view returns (string memory) {

87: function tokenName(address token) internal view returns (string memory) {
```

## [N-02] Use named imports instead of plain `import 'File.sol'`

Used regular imports on lines:
[Pair.sol/#L4-L11](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L4-L11)
[SafeERC20Namer.sol/#L4](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L4)
[LpToken.sol#L4-L5](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L4-L5)
[Caviar.sol#L4-L7](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L4-L7)

Instead of this, use named imports as you do on for example;

```solidity
File: Caviar.sol

import {Owned} from "solmate/auth/Owned.sol";

import {SafeERC20Namer} from "./lib/SafeERC20Namer.sol";
import {Pair} from "./Pair.sol";
```

## [N-03] Constants should be defined instead of using Magic Numbers

```solidity
File: Pair.sol

Line 400: return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);
Line 408: uint256 inputAmountWithFee = inputAmount * 997;
Line 409: return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);
```