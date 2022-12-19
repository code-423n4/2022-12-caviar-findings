## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Throughout the report some places might be denoted with audit tags to show the actual place affected.
- [FINDINGS](#findings)
- [Note on Gas estimates.](#note-on-gas-estimates)
- [Cache storage values in memory to minimize SLOADs](#cache-storage-values-in-memory-to-minimize-sloads)
  - [Pair.sol.withdraw(): closeTimestamp should be cached](#pairsolwithdraw-closetimestamp-should-be-cached)
- [Internal/Private functions only called once can be inlined to save gas](#internalprivate-functions-only-called-once-can-be-inlined-to-save-gas)
- [Multiple accesses of a mapping/array should use a local variable cache](#multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)
  - [Caviar.sol.create():  pairs\[nft\]\[baseToken\]\[merkleRoot\] should be cached in local storage](#caviarsolcreate--pairsnftbasetokenmerkleroot-should-be-cached-in-local-storage)
- [Avoid emitting the storage value here](#avoid-emitting-the-storage-value-here)
- [Duplicated require()/revert() checks should be refactored to a modifier or function](#duplicated-requirerevert-checks-should-be-refactored-to-a-modifier-or-function)
- [Using unchecked blocks to save gas](#using-unchecked-blocks-to-save-gas)
- [Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)](#using-unchecked-blocks-to-save-gas---increments-in-for-loop-can-be-unchecked---save-30-40-gas-per-loop-iteration)
- [Splitting require() statements that use \&\& saves gas - (saves 8 gas per \&\&)](#splitting-require-statements-that-use--saves-gas---saves-8-gas-per-)
- [Functions guaranteed to revert when called by normal users can be marked `payable`](#functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable)

## Note on Gas estimates.
I've tried to give the exact amount of gas being saved from running the included tests. Whenever the function is within the test coverage, the average gas before and after will be included, and often a diff of the code will also accompany this.

Some functions are not covered by the test cases or are internal/private functions. In this case, the gas can be estimated by looking at the opcodes involved. Whenever this method is applied the gas saved is stated as an approximate ie `~` symbol will be used, and most importantly there will be no benchmarks for gas before and after. 

The optimizer is set to run with the default runs(200).


## Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L359-L373
### Pair.sol.withdraw(): closeTimestamp should be cached
**Gas benchmarks**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 1211    | 4727   | 6556 | 7845 |
| After  | 1211    | 4680   | 6483 | 7851 |

```solidity
File: /src/Pair.sol
359:    function withdraw(uint256 tokenId) public {
360:        // check that the sender is the caviar owner
361:        require(caviar.owner() == msg.sender, "Withdraw: not owner");

363:        // check that the close period has been set
364:        require(closeTimestamp != 0, "Withdraw not initiated");

366:        // check that the close grace period has passed
367:        require(block.timestamp >= closeTimestamp, "Not withdrawable yet");

369:        // transfer the nft to the caviar owner
370:        ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenId);

372:        emit Withdraw(tokenId);
373:    }
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..a194cd4 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -360,11 +360,13 @@ contract Pair is ERC20, ERC721TokenReceiver {
         // check that the sender is the caviar owner
         require(caviar.owner() == msg.sender, "Withdraw: not owner");

+        uint256 _closeTimestamp = closeTimestamp;
+
         // check that the close period has been set
-        require(closeTimestamp != 0, "Withdraw not initiated");
+        require(_closeTimestamp != 0, "Withdraw not initiated");

         // check that the close grace period has passed
-        require(block.timestamp >= closeTimestamp, "Not withdrawable yet");
+        require(block.timestamp >= _closeTimestamp, "Not withdrawable yet");

         // transfer the nft to the caviar owner
         ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenId);
```

## Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L463
```solidity
File: /src/Pair.sol
463:    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Caviar.sol#L28-L43
### Caviar.sol.create():  pairs\[nft]\[baseToken]\[merkleRoot] should be cached in local storage
```solidity
File: /src/Caviar.sol
28:    function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {
29:        // check that the pair doesn't already exist
30:        require(pairs[nft][baseToken][merkleRoot] == address(0), "Pair already exists"); //@audit: First access

39:        // save the pair
40:        pairs[nft][baseToken][merkleRoot] = address(pair); //@audit: second access

43:    }
```

## Avoid emitting the storage value here
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead ie we can simply evaluate the expression directly

**Gas benchmarks**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 5644    | 28602   | 32429 | 32429 |
| After  | 5644    | 28570   | 32391 | 32391 |

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L341-L352
```solidity
File: /src/Pair.sol
341:    function close() public {
342:        // check that the sender is the caviar owner
343:        require(caviar.owner() == msg.sender, "Close: not owner");

345:        // set the close timestamp with a grace period
346:        closeTimestamp = block.timestamp + CLOSE_GRACE_PERIOD;

348:        // remove the pair from the Caviar contract
349:        caviar.destroy(nft, baseToken, merkleRoot);

351:        emit Close(closeTimestamp);
352:    }
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..b794686 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -348,7 +348,7 @@ contract Pair is ERC20, ERC721TokenReceiver {
         // remove the pair from the Caviar contract
         caviar.destroy(nft, baseToken, merkleRoot);

-        emit Close(closeTimestamp);
+        emit Close(block.timestamp + CLOSE_GRACE_PERIOD);
     }

```


## Duplicated require()/revert() checks should be refactored to a modifier or function
This saves deployement gas

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L343
```solidity
File: /src/Pair.sol
343:        require(caviar.owner() == msg.sender, "Close: not owner");

361:        require(caviar.owner() == msg.sender, "Withdraw: not owner");
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..e21efd6 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -36,6 +36,10 @@ contract Pair is ERC20, ERC721TokenReceiver {
     event Close(uint256 closeTimestamp);
     event Withdraw(uint256 tokenId);

+    modifier onlyOwner() {
+        require(caviar.owner() == msg.sender, "not owner");
+        _;
+    }
```

## Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L168
 **benchmarks**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 458    | 27949   | 32031 | 45624 |
| After  | 458    | 27936   | 31960 | 45624 |

```solidity
File: /src/Pair.sol
168:            uint256 refundAmount = maxInputAmount - inputAmount;
```
The operation `maxInputAmount - inputAmount` cannot underflow due to the check on [Line 157](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L157) that ensures that `maxInputAmount` is greater than `inputAmount` before perfoming the arithmetic operation

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..f0bfc81 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -165,7 +165,10 @@ contract Pair is ERC20, ERC721TokenReceiver {

         if (baseToken == address(0)) {
             // refund surplus eth
-            uint256 refundAmount = maxInputAmount - inputAmount;
+            uint256 refundAmount;
+            unchecked {
+                refundAmount = maxInputAmount - inputAmount;
+            }
             if (refundAmount > 0) msg.sender.safeTransferETH(refundAmount);
         } else {
             // transfer base tokens in
```

## Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```


**NB:** Most issues under this were in internal/private function as such exact gas estimate was not possible.


**Affected code**

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L238-L240
**Saves 330 gas on average**

|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 95561    | 123393   | 143961 | 145187 |
| After  | 95231    | 123063   | 143631 | 144857 |

```solidity
File: /src/Pair.sol
238:        for (uint256 i = 0; i < tokenIds.length; i++) {
239:            ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
240:        }
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..7159bef 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -235,8 +235,11 @@ contract Pair is ERC20, ERC721TokenReceiver {
         // *** Interactions *** //

         // transfer nfts from sender
-        for (uint256 i = 0; i < tokenIds.length; i++) {
+        for (uint256 i = 0; i < tokenIds.length;) {
             ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
+            unchecked {
+                ++i;
+            }
         }
```

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L258-L260
**Saves 330 gas on average**

|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 90130    | 92796   | 94130 | 94130 |
| After  | 89800    | 92466   | 93800 | 93800 |

```solidity
File: /src/Pair.sol
258:        for (uint256 i = 0; i < tokenIds.length; i++) {
259:            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
260:        }
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..4afad80 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -255,8 +255,11 @@ contract Pair is ERC20, ERC721TokenReceiver {
         // *** Interactions *** //

         // transfer nfts to sender
-        for (uint256 i = 0; i < tokenIds.length; i++) {
+        for (uint256 i = 0; i < tokenIds.length;) {
             ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
+            unchecked {
+                ++i;
+            }
         }
```

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L468-L471
```solidity
File: /src/Pair.sol
468:        for (uint256 i = 0; i < tokenIds.length; i++) {
469:            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
470:            require(isValid, "Invalid merkle proof");
471:        }
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..716b314 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -465,9 +465,12 @@ contract Pair is ERC20, ERC721TokenReceiver {
         if (merkleRoot == bytes23(0)) return;

         // validate merkle proofs against merkle root
-        for (uint256 i = 0; i < tokenIds.length; i++) {
+        for (uint256 i = 0; i < tokenIds.length;) {
             bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
             require(isValid, "Invalid merkle proof");
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L13
```solidity
File: /src/lib/SafeERC20Namer.sol
13:        for (uint256 j = 0; j < 32; j++) {
```

```diff
diff --git a/src/lib/SafeERC20Namer.sol b/src/lib/SafeERC20Namer.sol
index 2d8e57b..bc6c593 100644
--- a/src/lib/SafeERC20Namer.sol
+++ b/src/lib/SafeERC20Namer.sol
@@ -10,12 +10,15 @@ library SafeERC20Namer {
     function bytes32ToString(bytes32 x) private pure returns (string memory) {
         bytes memory bytesString = new bytes(32);
         uint256 charCount = 0;
-        for (uint256 j = 0; j < 32; j++) {
+        for (uint256 j = 0; j < 32;) {
             bytes1 char = x[j];
             if (char != 0) {
                 bytesString[charCount] = char;
                 charCount++;
             }
+            unchecked {
+                ++j;
+            }
         }
```

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L22
```solidity
File: /src/lib/SafeERC20Namer.sol
22:        for (uint256 j = 0; j < charCount; j++) {
```

```diff
diff --git a/src/lib/SafeERC20Namer.sol b/src/lib/SafeERC20Namer.sol
index 2d8e57b..79c2cab 100644
--- a/src/lib/SafeERC20Namer.sol
+++ b/src/lib/SafeERC20Namer.sol
@@ -21,6 +21,9 @@ library SafeERC20Namer {
         bytes memory bytesStringTrimmed = new bytes(charCount);
         for (uint256 j = 0; j < charCount; j++) {
             bytesStringTrimmed[j] = bytesString[j];
+            unchecked {
+                ++j;
+            }
         }
```

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L33
```solidity
File: /src/lib/SafeERC20Namer.sol
33:        for (uint256 i = 32; i < 64; i++) {
```

```diff
diff --git a/src/lib/SafeERC20Namer.sol b/src/lib/SafeERC20Namer.sol
index 2d8e57b..561651a 100644
--- a/src/lib/SafeERC20Namer.sol
+++ b/src/lib/SafeERC20Namer.sol
@@ -30,9 +30,12 @@ library SafeERC20Namer {
     function parseStringData(bytes memory b) private pure returns (string memory) {
         uint256 charCount = 0;
         // first parse the charCount out of the data
-        for (uint256 i = 32; i < 64; i++) {
+        for (uint256 i = 32; i < 64;) {
             charCount <<= 8;
             charCount += uint8(b[i]);
+            unchecked {
+                ++i;
+            }
         }
```

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L39
```solidity
File: /src/lib/SafeERC20Namer.sol
39:        for (uint256 i = 0; i < charCount; i++) {
```

```diff
diff --git a/src/lib/SafeERC20Namer.sol b/src/lib/SafeERC20Namer.sol
index 2d8e57b..918059b 100644
--- a/src/lib/SafeERC20Namer.sol
+++ b/src/lib/SafeERC20Namer.sol
@@ -38,6 +38,9 @@ library SafeERC20Namer {
         bytes memory bytesStringTrimmed = new bytes(charCount);
         for (uint256 i = 0; i < charCount; i++) {
             bytesStringTrimmed[i] = b[i + 64];
+            unchecked {
+                ++i;
+            }
         }
```

[see resource](https://github.com/ethereum/solidity/issues/10695)

## Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

**Proof**
**The following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```
**Execution cost**
21617 with optimization and using &&
21976 without optimization and using &&


After splitting the require statement
```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```
**Execution cost**
21609 with optimization and split require
21968 without optimization and using split require

https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L71
**Gas benchmarks**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 485    | 71815   | 84449 | 110061 |
| After  | 465    | 71809   | 84443 | 110053 |

```solidity
File: /src/Pair.sol
71:        require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

```diff
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..bab65c2 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -68,7 +68,8 @@ contract Pair is ERC20, ERC721TokenReceiver {
         // *** Checks *** //

         // check the token amount inputs are not zero
-        require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
+        require(baseTokenAmount > 0,  "Input token amount is zero");
+        require (fractionalTokenAmount > 0, "Input token amount is zero");
```


## Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.The extra opcodes avoided costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost


https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/LpToken.sol#L19
```solidity
File: /src/LpToken.sol
19:    function mint(address to, uint256 amount) public onlyOwner {

26:    function burn(address from, uint256 amount) public onlyOwner {

```