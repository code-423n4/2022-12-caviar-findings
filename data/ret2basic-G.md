# Caviar Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `unchecked{}` to suppress overflow/underflow check (7 instances)
2. Using `bool`s for storage incurs overhead (1 instance)
3. Empty blocks should be removed or emit something (2 instances)
4. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)
5. Use `private` instead of `public` for constants (2 instances)
6. Use shift right/left instead of division/multiplication if possible (1 instance)
7. Functions guaranteed to revert when called by normal users can be marked `payable` (2 instances)

Total 16 instances of 7 issues.

## 1. Use `unchecked{}` to suppress overflow/underflow check (7 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {

src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {

src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {

src/lib/SafeERC20Namer.sol::13 => for (uint256 j = 0; j < 32; j++) {

src/lib/SafeERC20Namer.sol::22 => for (uint256 j = 0; j < charCount; j++) {

src/lib/SafeERC20Namer.sol::33 => for (uint256 i = 32; i < 64; i++) {

src/lib/SafeERC20Namer.sol::39 => for (uint256 i = 0; i < charCount; i++) {
```

## 2. Using `bool`s for storage incurs overhead (1 instance)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
src/Pair.sol::469 => bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
```

## 3. Empty blocks should be removed or emit something (2 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
src/Caviar.sol::21 => constructor() Owned(msg.sender) {}

src/LpToken.sol::14 => {}
```

## 4. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (1 instance)

Instead of using operator && on single require check, using double `require()` checks can save more gas.

```solidity
src/Pair.sol::71 => require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

## 5. Use `private` instead of `public` for constants (2 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
src/Pair.sol::20 => uint256 public constant ONE = 1e18;

src/Pair.sol::21 => uint256 public constant CLOSE_GRACE_PERIOD = 7 days;
```

## 6. Use shift right/left instead of division/multiplication if possible (1 instance)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
src/lib/SafeERC20Namer.sol::55 => return Strings.toHexString(uint160(token) >> (160 - 4 * 4));
```

## 7. Functions guaranteed to revert when called by normal users can be marked `payable` (2 instances)

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
src/LpToken.sol::19 => function mint(address to, uint256 amount) public onlyOwner {

src/LpToken.sol::26 => function burn(address from, uint256 amount) public onlyOwner {
```
