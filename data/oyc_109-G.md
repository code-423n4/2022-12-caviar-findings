## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::12 => uint256 charCount = 0;
2022-12-caviar/src/lib/SafeERC20Namer.sol::13 => for (uint256 j = 0; j < 32; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::22 => for (uint256 j = 0; j < charCount; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::31 => uint256 charCount = 0;
2022-12-caviar/src/lib/SafeERC20Namer.sol::39 => for (uint256 i = 0; i < charCount; i++) {
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
```

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-12-caviar/src/Pair.sol::71 => require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

## [G-04] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-12-caviar/src/lib/SafeERC20Namer.sol::30 => function parseStringData(bytes memory b) private pure returns (string memory) {
```

## [G-05] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-12-caviar/src/Pair.sol::23 => address public immutable nft;
2022-12-caviar/src/Pair.sol::24 => address public immutable baseToken; // address(0) for ETH
2022-12-caviar/src/Pair.sol::25 => bytes32 public immutable merkleRoot;
2022-12-caviar/src/Pair.sol::26 => LpToken public immutable lpToken;
2022-12-caviar/src/Pair.sol::27 => Caviar public immutable caviar;
```

## [G-06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-12-caviar/src/LpToken.sol::19 => function mint(address to, uint256 amount) public onlyOwner {
2022-12-caviar/src/LpToken.sol::26 => function burn(address from, uint256 amount) public onlyOwner {
```

## [G-07] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::13 => for (uint256 j = 0; j < 32; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::17 => charCount++;
2022-12-caviar/src/lib/SafeERC20Namer.sol::22 => for (uint256 j = 0; j < charCount; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::33 => for (uint256 i = 32; i < 64; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::39 => for (uint256 i = 0; i < charCount; i++) {
```

## [G-08] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-12-caviar/src/Pair.sol::448 => balanceOf[from] -= amount;
2022-12-caviar/src/Pair.sol::453 => balanceOf[to] += amount;
2022-12-caviar/src/lib/SafeERC20Namer.sol::35 => charCount += uint8(b[i]);
```

## [G-09] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-12-caviar/src/Caviar.sol::30 => require(pairs[nft][baseToken][merkleRoot] == address(0), "Pair already exists");
2022-12-caviar/src/Caviar.sol::51 => require(msg.sender == pairs[nft][baseToken][merkleRoot], "Only pair can destroy itself");
2022-12-caviar/src/Pair.sol::71 => require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
2022-12-caviar/src/Pair.sol::74 => require(baseToken == address(0) ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");
2022-12-caviar/src/Pair.sol::80 => require(lpTokenAmount >= minLpTokenAmount, "Slippage: lp token amount out");
2022-12-caviar/src/Pair.sol::117 => require(baseTokenOutputAmount >= minBaseTokenOutputAmount, "Slippage: base token amount out");
2022-12-caviar/src/Pair.sol::120 => require(fractionalTokenOutputAmount >= minFractionalTokenOutputAmount, "Slippage: fractional token out");
2022-12-caviar/src/Pair.sol::151 => require(baseToken == address(0) ? msg.value == maxInputAmount : msg.value == 0, "Invalid ether input");
2022-12-caviar/src/Pair.sol::157 => require(inputAmount <= maxInputAmount, "Slippage: amount in");
2022-12-caviar/src/Pair.sol::189 => require(outputAmount >= minOutputAmount, "Slippage: amount out");
2022-12-caviar/src/Pair.sol::224 => require(closeTimestamp == 0, "Wrap: closed");
2022-12-caviar/src/Pair.sol::343 => require(caviar.owner() == msg.sender, "Close: not owner");
2022-12-caviar/src/Pair.sol::361 => require(caviar.owner() == msg.sender, "Withdraw: not owner");
2022-12-caviar/src/Pair.sol::364 => require(closeTimestamp != 0, "Withdraw not initiated");
2022-12-caviar/src/Pair.sol::367 => require(block.timestamp >= closeTimestamp, "Not withdrawable yet");
2022-12-caviar/src/Pair.sol::470 => require(isValid, "Invalid merkle proof");
```

## [G-10] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::33 => for (uint256 i = 32; i < 64; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::39 => for (uint256 i = 0; i < charCount; i++) {
```

## [G-11] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2022-12-caviar/src/Pair.sol::71 => require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

## [G-12] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-12-caviar/src/Caviar.sol::28 => function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {
2022-12-caviar/src/Caviar.sol::49 => function destroy(address nft, address baseToken, bytes32 merkleRoot) public {
2022-12-caviar/src/Pair.sol::310 => function nftBuy(uint256[] calldata tokenIds, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {
2022-12-caviar/src/Pair.sol::341 => function close() public {
2022-12-caviar/src/Pair.sol::359 => function withdraw(uint256 tokenId) public {
2022-12-caviar/src/Pair.sol::390 => function price() public view returns (uint256) {
```

## [G-13] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-12-caviar/src/Pair.sol::435 => function removeQuote(uint256 lpTokenAmount) public view returns (uint256, uint256) {
```

## [G-14] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-12-caviar/src/Caviar.sol::19 => mapping(address => mapping(address => mapping(bytes32 => address))) public pairs;
```

## [G-15] Use selfbalance()

Use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas.

```
2022-12-caviar/src/Pair.sol::479 => ? address(this).balance - msg.value // subtract the msg.value if the base token is ETH
```

## [G-16] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-12-caviar/src/Pair.sol::463 => function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
2022-12-caviar/src/lib/SafeERC20Namer.sol::76 => function tokenSymbol(address token) internal view returns (string memory) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::87 => function tokenName(address token) internal view returns (string memory) {
```

## [G-17] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2022-12-caviar/src/lib/SafeERC20Namer.sol::76 => function tokenSymbol(address token) internal view returns (string memory) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::87 => function tokenName(address token) internal view returns (string memory) {
```