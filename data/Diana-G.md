
## G-01 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 7 instances of this issue_

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
File: src/Pair.sol

238: for (uint256 i = 0; i < tokenIds.length; i++) {
258: for (uint256 i = 0; i < tokenIds.length; i++) {
468: for (uint256 i = 0; i < tokenIds.length; i++) {
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```
File: src/lib/SafeERC20Namer.sol

13: for (uint256 j = 0; j < 32; j++) {
22: for (uint256 j = 0; j < charCount; j++) {
33: for (uint256 i = 32; i < 64; i++) {
39: for (uint256 i = 0; i < charCount; i++) {
```

---------------

## G-02 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
File: src/Pair.sol

448: balanceOf[from] -= amount;
453: balanceOf[to] += amount;
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```
File: src/lib/SafeERC20Namer.sol

35: charCount += uint8(b[i]);
```

-----

## G-03 SPLITTING REQURE() STATEMENTS THAT USE && SAVES GAS

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
File: main/src/Pair.sol

71: require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

---------

## G-04 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
File: src/Pair.sol

463: function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

```
File: src/lib/SafeERC20Namer.sol

87: function tokenName(address token) internal view returns (string memory) {
```

------------

## G-05 DUPLICATED REQUIRE() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

```
File: src/Pair.sol

343: require(caviar.owner() == msg.sender, "Close: not owner");
361: require(caviar.owner() == msg.sender, "Withdraw: not owner");
```

--------
