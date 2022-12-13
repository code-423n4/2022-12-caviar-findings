# Inconsistent docs on `SafeERC20Namer`

The documentation of `SafeERC20Namer` is inconsistent with its implementation, due to the modification of Uniswap's version. In their case, the function `addressToSymbol` would return the first 6 hex of the address string. In Caviar's case, it's the first 4 hex. Fix:

```diff
diff --git a/src/lib/SafeERC20Namer.sol b/src/lib/SafeERC20Namer.sol
index 2d8e57b..411c1b4 100644
--- a/src/lib/SafeERC20Namer.sol
+++ b/src/lib/SafeERC20Namer.sol
@@ -77,7 +77,7 @@ library SafeERC20Namer {
         // 0x95d89b41 = bytes4(keccak256("symbol()"))
         string memory symbol = callAndParseStringReturn(token, 0x95d89b41);
         if (bytes(symbol).length == 0) {
-            // fallback to 6 uppercase hex of address
+            // fallback to 4 uppercase hex of address
             return addressToSymbol(token);
         }
         return symbol;

```