# 1. `SafeERC20Namer.addressToSymbol` implementation does not adhere to specification, causing LP symbol/name to be incorrect in some cases

By fuzzying the existing test testItSetsSymbolsAndNames with Foundry, the test yields an error on the function `SafeERC20Namer.addressToSymbol`. When this function is called on the token address `0`, it will incorrectly return `0x00`, and not `0x0000` as expected from the specification. The issue is related to `Strings.toHexString` used under the hood.

## Test case

```diff
diff --git a/test/Caviar/Create.t.sol b/test/Caviar/Create.t.sol
index 965a5aa..2baeb32 100644
--- a/test/Caviar/Create.t.sol
+++ b/test/Caviar/Create.t.sol
@@ -6,6 +6,29 @@ import "forge-std/console.sol";
 
 import "../shared/Fixture.t.sol";
 
+library Strings2 {
+
+    ///@dev converts bytes array to its ASCII hex string representation
+    /// TODO: Definitely more efficient way to do this by processing multiple (16?) bytes at once
+    /// but really a helper function for the tests, efficiency not key.
+    function toHexString(bytes memory input) public pure returns (string memory) {
+        require(input.length < type(uint256).max / 2 - 1);
+        bytes16 symbols = "0123456789abcdef";
+        bytes memory hex_buffer = new bytes(2 * input.length + 2);
+        hex_buffer[0] = "0";
+        hex_buffer[1] = "x";
+
+        uint pos = 2;
+        uint256 length = input.length;
+        for (uint i = 0; i < length; ++i) {
+            uint _byte = uint8(input[i]);
+            hex_buffer[pos++] = symbols[_byte >> 4];
+            hex_buffer[pos++] = symbols[_byte & 0xf];
+        }
+        return string(hex_buffer);
+    }
+}
+
 contract CreateTest is Fixture {
     event Create(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
 
@@ -41,6 +64,17 @@ contract CreateTest is Fixture {
         assertEq(lpToken.name(), "0xbeef:0xcafe LP token", "Should have set lp name");
     }
 
+    function testItSetsSymbolsAndNamesFuzzy(address token) public {
+        Pair pair = c.create(token, token, bytes32(0));
+
+        string[] memory cmds = new string[](3);
+        cmds[0] = "node";
+        cmds[1] = "test/testItSetsSymbolsAndNamesFuzzy.js";
+        cmds[2] = Strings2.toHexString(abi.encode(token));
+        bytes memory result = vm.ffi(cmds);
+        assertEq(pair.symbol(), string(result));
+    }
+
     function testItSavesPair() public {
         // arrange
         address nft = address(0xbeef);
diff --git a/test/testItSetsSymbolsAndNamesFuzzy.js b/test/testItSetsSymbolsAndNamesFuzzy.js
new file mode 100644
index 0000000..8dbc9c5
--- /dev/null
+++ b/test/testItSetsSymbolsAndNamesFuzzy.js
@@ -0,0 +1,6 @@
+function main(token) {
+  var address = token.substring(token.length-40, token.length)
+  return `f0x${address.substring(0, 4)}`
+}
+
+console.log(main(process.argv[2]));
\ No newline at end of file

```

Run with

```
forge test --ffi --match-test testItSetsSymbolsAndNamesFuzzy
```

# 2. Inconsistent docs on `SafeERC20Namer`