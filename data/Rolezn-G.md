## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 3 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 7 | 245 |
| [GAS&#x2011;3](#GAS&#x2011;3) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 4 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | Splitting `require()` Statements That Use `&&` Saves Gas | 1 | 9 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Public Functions To External | 18 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Optimize names to save gas | 3 | 66 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Using fixed bytes is cheaper than using `string` | 2 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Superfluous event fields | 1 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | `internal` functions only called once can be inlined to save gas | 3 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Setting the `constructor` to `payable` | 3 | 39 |
| [GAS&#x2011;11](#GAS&#x2011;11) | Functions guaranteed to revert when called by normal users can be marked `payable` | 2 | 42 |
| [GAS&#x2011;12](#GAS&#x2011;12) | Using `unchecked` blocks to save gas | 1 | 136 |

Total: 48 contexts over 12 issues

## Gas Optimizations





### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
448: balanceOf[from] -= amount;
453: balanceOf[to] += amount;

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L448

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L453



```solidity
35: charCount += uint8(b[i]);

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L35





### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
238: for (uint256 i = 0; i < tokenIds.length; i++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L238

```solidity
258: for (uint256 i = 0; i < tokenIds.length; i++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L258

```solidity
468: for (uint256 i = 0; i < tokenIds.length; i++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L468

```solidity
13: for (uint256 j = 0; j < 32; j++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L13

```solidity
22: for (uint256 j = 0; j < charCount; j++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L22

```solidity
33: for (uint256 i = 32; i < 64; i++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L33

```solidity
39: for (uint256 i = 0; i < charCount; i++) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L39





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
51: require(msg.sender == pairs[nft][baseToken][merkleRoot], "Only pair can destroy itself");
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L51

```solidity
80: require(lpTokenAmount >= minLpTokenAmount, "Slippage: lp token amount out");
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L80

```solidity
117: require(baseTokenOutputAmount >= minBaseTokenOutputAmount, "Slippage: base token amount out");
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L117

```solidity
120: require(fractionalTokenOutputAmount >= minFractionalTokenOutputAmount, "Slippage: fractional token out");
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L120






### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
	require(version == 1);
	require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```solidity
71: require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L71





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L28

```solidity
function destroy(address nft, address baseToken, bytes32 merkleRoot) public {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L49

```solidity
function mint(address to, uint256 amount) public onlyOwner {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L19

```solidity
function burn(address from, uint256 amount) public onlyOwner {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L26

```solidity
function buy(uint256 outputAmount, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L147

```solidity
function sell(uint256 inputAmount, uint256 minOutputAmount) public returns (uint256 outputAmount) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L182

```solidity
function unwrap(uint256[] calldata tokenIds) public returns (uint256 fractionalTokenAmount) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L248

```solidity
function nftAdd(
        uint256 baseTokenAmount,
        uint256[] calldata tokenIds,
        uint256 minLpTokenAmount,
        bytes32[][] calldata proofs
    ) public payable returns (uint256 lpTokenAmount) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L275

```solidity
function nftBuy(uint256[] calldata tokenIds, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L310

```solidity
function close() public {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L341

```solidity
function withdraw(uint256 tokenId) public {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L359

```solidity
function baseTokenReserves() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L379

```solidity
function fractionalTokenReserves() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L383

```solidity
function price() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L390

```solidity
function buyQuote(uint256 outputAmount) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L398

```solidity
function sellQuote(uint256 inputAmount) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L406

```solidity
function addQuote(uint256 baseTokenAmount, uint256 fractionalTokenAmount) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L417

```solidity
function removeQuote(uint256 lpTokenAmount) public view returns (uint256, uint256) {
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L435






### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

```solidity
File: .\Projects\caviar202212\2022-12-caviar\src\Caviar.sol
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol

```solidity
File: .\Projects\caviar202212\2022-12-caviar\src\LpToken.sol
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol

```solidity
File: .\Projects\caviar202212\2022-12-caviar\src\Pair.sol
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Using fixed bytes is cheaper than using `string`

As a rule of thumb, use `bytes` for arbitrary-length raw byte data and string for arbitrary-length `string` (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper.

#### <ins>Proof Of Concept</ins>


```solidity
33: string memory baseTokenSymbol = baseToken == address(0) ? "ETH" : baseToken.tokenSymbol();
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L33

```solidity
36: string memory pairSymbol = string.concat(nftSymbol, ":", baseTokenSymbol);
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L36






### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Superfluous event fields

`block.number` and `block.timestamp` are added to the event information by default, so adding them manually will waste additional gas.

#### <ins>Proof Of Concept</ins>


```solidity
36: event Close(uint256 closeTimestamp);
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L36





### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
463: function _validateTokenIds

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L463

```solidity
76: function tokenSymbol

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L76

```solidity
87: function tokenName

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L87






### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
21: constructor() Owned(msg.sender)
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L21

```solidity
11: constructor(string memory pairSymbol)
        Owned(msg.sender)
        ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L11

```solidity
39: constructor(
        address _nft,
        address _baseToken,
        bytes32 _merkleRoot,
        string memory pairSymbol,
        string memory nftName,
        string memory nftSymbol
    ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18)
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L39





### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

#### <ins>Proof Of Concept</ins>

```solidity
19: function mint(address to, uint256 amount) public onlyOwner {

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L19

```solidity
26: function burn(address from, uint256 amount) public onlyOwner {

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L26



#### <ins>Recommended Mitigation Steps</ins>
Functions guaranteed to revert when called by normal users can be marked payable.



### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
168: uint256 refundAmount = maxInputAmount - inputAmount;

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L168





