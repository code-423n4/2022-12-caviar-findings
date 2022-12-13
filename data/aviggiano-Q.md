# 1. Inconsistent docs on `SafeERC20Namer`

The documentation of `SafeERC20Namer` is inconsistent with its implementation, due to the modification of Uniswap's version. In their case, the function `addressToSymbol` would return the first [6 hex](https://github.com/Uniswap/solidity-lib/blob/master/contracts/libraries/SafeERC20Namer.sol#L51) of the address string. In Caviar's case, it's the first [4 hex](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L53). Fix:

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

# 2. Floating Pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively. See [SWC-103](https://swcregistry.io/docs/SWC-103). Fix: 

```diff
diff --git a/src/Caviar.sol b/src/Caviar.sol
index 674ed7b..bd7adb2 100644
--- a/src/Caviar.sol
+++ b/src/Caviar.sol
@@ -1,5 +1,5 @@
 // SPDX-License-Identifier: MIT
-pragma solidity ^0.8.17;
+pragma solidity 0.8.17;
 
 import "solmate/auth/Owned.sol";
 
diff --git a/src/LpToken.sol b/src/LpToken.sol
index 345b6bd..63f8655 100644
--- a/src/LpToken.sol
+++ b/src/LpToken.sol
@@ -1,5 +1,5 @@
 // SPDX-License-Identifier: MIT
-pragma solidity ^0.8.17;
+pragma solidity 0.8.17;
 
 import "solmate/auth/Owned.sol";
 import "solmate/tokens/ERC20.sol";
diff --git a/src/Pair.sol b/src/Pair.sol
index 185d25c..d99182b 100644
--- a/src/Pair.sol
+++ b/src/Pair.sol
@@ -1,5 +1,5 @@
 // SPDX-License-Identifier: MIT
-pragma solidity ^0.8.17;
+pragma solidity 0.8.17;
 
 import "solmate/tokens/ERC20.sol";
 import "solmate/tokens/ERC721.sol";
diff --git a/src/lib/SafeERC20Namer.sol b/src/lib/SafeERC20Namer.sol
index 2d8e57b..320353b 100644
--- a/src/lib/SafeERC20Namer.sol
+++ b/src/lib/SafeERC20Namer.sol
@@ -1,5 +1,5 @@
 // SPDX-License-Identifier: MIT
-pragma solidity ^0.8.17;
+pragma solidity 0.8.17;
 
 import "openzeppelin/utils/Strings.sol";
 

```



