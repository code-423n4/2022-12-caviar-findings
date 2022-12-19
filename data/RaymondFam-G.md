## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: Pair.sol#L477-L481](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L477-L481)

```
-    function _baseTokenReserves() internal view returns (uint256) {
+    function _baseTokenReserves() internal view returns (uint256 baseTokenReserves) {
-        return baseToken == address(0)
+        baseTokenReserves baseToken == address(0)
            ? address(this).balance - msg.value // subtract the msg.value if the base token is ETH
            : ERC20(baseToken).balanceOf(address(this));
    }
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For example, the `+=` instance below may be refactored as follows:

[File: Pair.sol#L453](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L453)

```diff
-            balanceOf[to] += amount;
+            balanceOf[to] = balanceOf[to] + amount;
```
Similarly, the `-=` instance below may be refactored as follows:

[File: Pair.sol#L448](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448)

```diff
-        balanceOf[from] -= amount;
+        balanceOf[from] = balanceOf[from] - amount;
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: Pair.sol#L468-L471](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468-L471)

```diff
-        for (uint256 i = 0; i < tokenIds.length; i++) {
+        for (uint256 i = 0; i < tokenIds.length;) {
            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
            require(isValid, "Invalid merkle proof");

+            unchecked {
+                ++i;
+            }
        }
```
## State Variables Repeatedly Read Should be Cached
SLOADs cost 100 gas each after the 1st one whereas MLOADs/MSTOREs only incur 3 gas each. As such, storage values read multiple times should be cached in the stack memory the first time (costing only 1 SLOAD) and then re-read from this cache to avoid multiple SLOADs.

For instance, `closeTimestamp` in the code block below could be cached as follows:

[File: Pair.sol#L364-L367](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L364-L367)

```diff
+        uint256 _closeTimestamp = closeTimestamp; // SLOAD 1

-        require(closeTimestamp != 0, "Withdraw not initiated");
+        require(_closeTimestamp != 0, "Withdraw not initiated"); // MLOAD 1

        // check that the close grace period has passed
-        require(block.timestamp >= closeTimestamp, "Not withdrawable yet");
+        require(block.timestamp >= _closeTimestamp, "Not withdrawable yet"); // MLOAD 2
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: Pair.sol#L80](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L80)

```diff
-        require(lpTokenAmount >= minLpTokenAmount, "Slippage: lp token amount out");
+        require(lpTokenAmount > minLpTokenAmount - 1, "Slippage: lp token amount out");
```
Similarly, consider replacing `<=` with the strict counterpart `<` in the following inequality instance, as an example:

[File: Pair.sol#L157](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L157)

```diff
-        require(inputAmount <= maxInputAmount, "Slippage: amount in");
+        require(inputAmount < maxInputAmount + 1, "Slippage: amount in");
```
## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here is an instance entailed:

[File: Pair.sol#L71](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71)

```
        require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```
## Payable access control functions costs less gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

For instance, the code line below may be refactored as follows:

[File: Pair.sol#L341](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341)

```diff
-    function close() public {
+    function close() public payable {
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
  version: "0.8.16",
  settings: {
    optimizer: {
      enabled: true,
      runs: 1000,
    },
  },
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.