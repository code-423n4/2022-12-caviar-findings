

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-12-caviar/src/Pair.sol::28 => uint256 public closeTimestamp;
```



###  [G02] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
```




###  [G03] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::22 => for (uint256 j = 0; j < charCount; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::33 => for (uint256 i = 32; i < 64; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::39 => for (uint256 i = 0; i < charCount; i++) {
```







### [G04] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-12-caviar/src/Pair.sol::71 => require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```




### [G05] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
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




### [G06] `++i` costs less gas than `i++`, especially when it’s used in for loops (`--i`/`i--` too)


#### Findings:
```
2022-12-caviar/src/Pair.sol::238 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::258 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/Pair.sol::468 => for (uint256 i = 0; i < tokenIds.length; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::13 => for (uint256 j = 0; j < 32; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::17 => charCount++;
2022-12-caviar/src/lib/SafeERC20Namer.sol::22 => for (uint256 j = 0; j < charCount; j++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::33 => for (uint256 i = 32; i < 64; i++) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::35 => charCount += uint8(b[i]);
2022-12-caviar/src/lib/SafeERC20Namer.sol::39 => for (uint256 i = 0; i < charCount; i++) {
```




### [G07] Splitting `require()` statements that use `&&` Cost gas

#### Impact
See [this](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) issue for an example
#### Findings:
```
2022-12-caviar/src/Pair.sol::71 => require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```




### [G08] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-12-caviar/src/lib/SafeERC20Namer.sol::49 => return Strings.toHexString(uint160(token));
2022-12-caviar/src/lib/SafeERC20Namer.sol::55 => return Strings.toHexString(uint160(token) >> (160 - 4 * 4));
```






### [G09] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-12-caviar/src/Pair.sol::74 => require(baseToken == address(0) ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");
```






### [G10] Use custom errors rather than `revert()`/`require()` strings to save deployment gas



#### Findings:
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




### [G11] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-12-caviar/src/LpToken.sol::19 => function mint(address to, uint256 amount) public onlyOwner {
2022-12-caviar/src/LpToken.sol::26 => function burn(address from, uint256 amount) public onlyOwner {
```




### [G12] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-12-caviar/src/Caviar.sol::21 => constructor() Owned(msg.sender) {}
2022-12-caviar/src/LpToken.sol::14 => {}
```




