## Summary

### Gas Optimizations List
| Number | Optimization Details | Context |
|:---:|:-------| :-----:|
| [G-01] | Use ``assembly`` to write _address storage values_ | 2 |
| [G-02] | Setting the _constructor_ to `payable`| 3 |
| [G-03] | Functions guaranteed to revert_ when callled by normal users can be marked `payable` | 2 |
| [G-04] | Empty blocks should be removed or emit something  | 2 |
| [G-05] | Optimize names to save gas| |
| [G-06] | Import only what you need  | 13 |
| [G-07] | `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables  | 3 |
| [G-08] | Comparison operators  | 5 |
| [G-09] |Use ``double require`` instead of using ``&&`` | 1 |
| [G-10] | The solady Library's Ownable contract is significantly gas-optimized, which can be used| 1 |
| [G-11] | Separate Ether and ER20 controls into two separate internal functions| 1 |

Total 11 issues

### [G-01] Use ``assembly`` to write _address storage values_ 

```solidity
2 results - 1 file

src\Pair.sol:
  39     constructor(
  40         address _nft,
  41         address _baseToken,
  42         bytes32 _merkleRoot,
  43         string memory pairSymbol,
  44         string memory nftName,
  45         string memory nftSymbol
  46      ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
  47:         nft = _nft;
  48:         baseToken = _baseToken; // use address(0) for native ETH
  49          merkleRoot = _merkleRoot;
  50:         lpToken = new LpToken(pairSymbol);
  51:         caviar = Caviar(msg.sender);
```

**Recommendation Code:**
```diff
  39       constructor(
  40           address _nft,
  41           address _baseToken,
  42           bytes32 _merkleRoot,
  43           string memory pairSymbol,
  44           string memory nftName,
  45           string memory nftSymbol
  46       ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
- 47:            nft = _nft;
- 48:            baseToken = _baseToken; // use address(0) for native ETH
+                    assembly {
+ 47:                  sstore(nft.slot,_nft)
+ 48:                  sstore(baseToken,_baseToken) // use address(0) for native ETH
+                    }
  49              merkleRoot = _merkleRoot;
  50              lpToken = new LpToken(pairSymbol);
  51              caviar = Caviar(msg.sender);
```

### [G-02] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity
3 results - 3 files

src\Caviar.sol:
  20  
  21:     constructor() Owned(msg.sender) {}
  22  

src\LpToken.sol:
  10  contract LpToken is Owned, ERC20 {
  11:     constructor(string memory pairSymbol)
  12          Owned(msg.sender)

src\Pair.sol:
  38  
  39:     constructor(
  40          address _nft,
```

**Recommendation:**
Set the constructor to ```payable```

### [G-03]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
2 results - 1 file

src\LpToken.sol:
  19:     function mint(address to, uint256 amount) public onlyOwner {
  26:     function burn(address from, uint256 amount) public onlyOwner {

```

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyOwner``` functions)


### [G-04] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
2 results - 2 files

src\Caviar.sol:
  20  
  21:     constructor() Owned(msg.sender) {}
  22  

src\LpToken.sol:
  10  contract LpToken is Owned, ERC20 {
  11:     constructor(string memory pairSymbol)
  12:         Owned(msg.sender)
  13:         ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
  14:     {}
```
### [G-05] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` Pair.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

Pair.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
505fb46c  =>  add(uint256,uint256,uint256)
7ed72fda  =>  remove(uint256,uint256,uint256)
d6febde8  =>  buy(uint256,uint256)
d79875eb  =>  sell(uint256,uint256)
694ac6cf  =>  wrap(uint256[],bytes32[][])
63ac2a4c  =>  unwrap(uint256[])
68b96890  =>  nftAdd(uint256,uint256[],uint256,bytes32[][])
c4347920  =>  nftRemove(uint256,uint256,uint256[])
40c9adbe  =>  nftBuy(uint256[],uint256)
4e4d7c00  =>  nftSell(uint256[],uint256,bytes32[][])
43d726d6  =>  close()
2e1a7d4d  =>  withdraw(uint256)
203ae565  =>  baseTokenReserves()
fceacc83  =>  fractionalTokenReserves()
a035b1fe  =>  price()
168585e5  =>  buyQuote(uint256)
d7a2e4c9  =>  sellQuote(uint256)
ceb17898  =>  addQuote(uint256,uint256)
037d785b  =>  removeQuote(uint256)
cb712535  =>  _transferFrom(address,address,uint256)
eb7439df  =>  _validateTokenIds(uint256[],bytes32[][])
9f9629f8  =>  _baseTokenReserves()
```
### [G-06] Import only what you need

Importing only what is needed is ideal for gas optimization.

```solidity
13 results - 4 files

src\Caviar.sol:  
  4: import "solmate/auth/Owned.sol";
  6: import "./lib/SafeERC20Namer.sol";
  7: import "./Pair.sol";

src\LpToken.sol:
  4: import "solmate/auth/Owned.sol";
  5: import "solmate/tokens/ERC20.sol";

src\Pair.sol:
   4: import "solmate/tokens/ERC20.sol";
   5: import "solmate/tokens/ERC721.sol";
   6: import "solmate/utils/MerkleProofLib.sol";
   7: import "solmate/utils/SafeTransferLib.sol";
   8: import "openzeppelin/utils/math/Math.sol";
  10: import "./LpToken.sol";
  11: import "./Caviar.sol";
```

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`
### [G-07] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

```solidity
3 results - 2 files

src\Pair.sol:
  453:    balanceOf[to] += amount;
  448:    balanceOf[from] -= amount;

src\lib\SafeERC20Namer.sol:
  35:      charCount += uint8(b[i]);
```

```diff
src\Pair.sol:#L453
- 453:   balanceOf[to] += amount;
+ 453:   balanceOf[to] = balanceOf[to] + amount;

```

`x += y (x -= y)` costs more gas than `x = x  + y (x = x - y)` for state variables.

### [G-08] Comparison operators

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `> and =`. Using strict comparison operators hence saves gas

**Context:**
```solidity
5 results - 1 file

src\Pair.sol:
   80:    require(lpTokenAmount >= minLpTokenAmount, "Slippage: lp token amount out");
  117:    require(baseTokenOutputAmount >= minBaseTokenOutputAmount, "Slippage: base token amount out");
  120:    require(fractionalTokenOutputAmount >= minFractionalTokenOutputAmount, "Slippage: fractional token out");
  189:    require(outputAmount >= minOutputAmount, "Slippage: amount out");
  367:    require(block.timestamp >= closeTimestamp, "Not withdrawable yet");

  157:    require(inputAmount <= maxInputAmount, "Slippage: amount in"); 

```

**Recommendation:** 
Replace <= with <, and >= with >. Do not forget to increment/decrement the compared variable.

### [G-09] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

**Context:** 
```solidity
1 result - 1 file

src\Pair.sol:
  71:         require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

```

**Recommendation Code:**
 ```diff
src\Pair.sol#L71
- 71:   require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
+ 71:   require(baseTokenAmount > 0, "Input token amount is zero");
+ 	require(fractionalTokenAmount > 0, "Input token amount is zero");

```

### [G-10] The solady Library's Ownable contract is significantly gas-optimized, which can be used

The project uses the ` onlyOwner ` authorization model I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol

### [G-11] Separate Ether and ER20 controls into two separate internal functions

The _baseTokenReserves() function is an internal function and is used by other functions. It controls both ERC20 and Ether functions, which means higher gas.

Separate the Ether and ERC20 checks into two separate internal functions and call them in two separate functions, so no extra checks will be made.

```solidity
src/Pair.sol:
      ///      xyk math is correct in the buy() and add() functions.
     function _baseTokenReserves() internal view returns (uint256) {
         return baseToken == address(0)
             ? address(this).balance - msg.value // subtract the msg.value if the base token is ETH
             : ERC20(baseToken).balanceOf(address(this));
     }

		
```

