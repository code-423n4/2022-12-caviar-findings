- [Gas](#gas)
    - [**1. Remove unuseful index**](#1-remove-unuseful-index)
        - [forge test --gas-report](#forge-test---gas-report)
    - [**2. Optimize bytes32ToString**](#2-optimize-bytes32tostring)
        - [forge test --gas-report](#forge-test---gas-report)
    - [**3. Avoid compound assignment operator in state variables**](#3-avoid-compound-assignment-operator-in-state-variables)
        - [forge test --gas-report](#forge-test---gas-report)
    - [**4. Avoid unused returns**](#4-avoid-unused-returns)
        - [forge test --gas-report](#forge-test---gas-report)

# Gas

## **1. Remove unuseful index**

```diff
-   event Create(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
-   event Destroy(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
+   event Create(address indexed nft, address indexed baseToken, bytes32 merkleRoot);
+   event Destroy(address indexed nft, address indexed baseToken, bytes32 merkleRoot);
```

### forge test --gas-report

BEFORE in red, AFTER in green:

```diff
    ╭────────────────────────────────┬─────────────────┬─────────┬─────────┬─────────┬─────────╮
    │ src/Caviar.sol:Caviar contract ┆                 ┆         ┆         ┆         ┆         │
    ╞════════════════════════════════╪═════════════════╪═════════╪═════════╪═════════╪═════════╡
    │ Deployment Cost                ┆ Deployment Size ┆         ┆         ┆         ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ 4625247                        ┆ 23078           ┆         ┆         ┆         ┆         │
+   │ 4629047                        ┆ 23097           ┆         ┆         ┆         ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
    │ Function Name                  ┆ min             ┆ avg     ┆ median  ┆ max     ┆ # calls │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ create                         ┆ 913             ┆ 3395399 ┆ 3413705 ┆ 3472634 ┆ 190     │
+   │ create                         ┆ 913             ┆ 3395311 ┆ 3413616 ┆ 3472545 ┆ 190     │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ destroy                        ┆ 2511            ┆ 5619    ┆ 6351    ┆ 6351    ┆ 10      │
+   │ destroy                        ┆ 2437            ┆ 5553    ┆ 6277    ┆ 6277    ┆ 10      │
    ╰────────────────────────────────┴─────────────────┴─────────┴─────────┴─────────┴─────────╯
```

- [Caviar.sol:15-17](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Caviar.sol#L15-L17)

## **2. Optimize `bytes32ToString`**

Changing the method to be like:

```js
    function bytes32ToString(bytes32 x) private pure returns (string memory) {
        unchecked
        {
            uint256 charCount;
            while(x[charCount] != 0 && charCount < 32) ++charCount;
            bytes memory bytesStringTrimmed = new bytes(charCount);
            for (uint256 j; j < charCount; ++j) {
                bytesStringTrimmed[j] = x[j];
            }

            return string(bytesStringTrimmed);
        }
    }
```

### forge test --gas-report

It's possible to optimize the call `Caviar.create`:

BEFORE in red, AFTER in green:

```diff
    ╭────────────────────────────────┬─────────────────┬─────────┬─────────┬─────────┬─────────╮
    │ src/Caviar.sol:Caviar contract ┆                 ┆         ┆         ┆         ┆         │
    ╞════════════════════════════════╪═════════════════╪═════════╪═════════╪═════════╪═════════╡
    │ Deployment Cost                ┆ Deployment Size ┆         ┆         ┆         ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ 4625247                        ┆ 23078           ┆         ┆         ┆         ┆         │
+   │ 4594403                        ┆ 22924           ┆         ┆         ┆         ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
    │ Function Name                  ┆ min             ┆ avg     ┆ median  ┆ max     ┆ # calls │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ create                         ┆ 913             ┆ 3395399 ┆ 3413705 ┆ 3472634 ┆ 190     │
+   │ create                         ┆ 913             ┆ 3395397 ┆ 3413705 ┆ 3472490 ┆ 190     │
    ╰────────────────────────────────┴─────────────────┴─────────┴─────────┴─────────┴─────────╯
```

**Affected source code:**

- [SafeERC20Namer.sol#L11-L28](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L11-L28)

## **3. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint256 private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
Uint256 private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

### forge test --gas-report

BEFORE in red, AFTER in green:

```diff
    ╭────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
    │ src/Pair.sol:Pair contract ┆                 ┆        ┆        ┆        ┆         │
    ╞════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
    │ Deployment Cost            ┆ Deployment Size ┆        ┆        ┆        ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ 3342647                    ┆ 19392           ┆        ┆        ┆        ┆         │
+   │ 3341239                    ┆ 19385           ┆        ┆        ┆        ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
    │ Function Name              ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ remove                     ┆ 13769           ┆ 59863  ┆ 77115  ┆ 83615  ┆ 10      │
+   │ remove                     ┆ 13769           ┆ 54784  ┆ 67265  ┆ 83641  ┆ 10      │
    ╰────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
```

**Affected source code:**

- [Pair.sol:448](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L448)
- [Pair.sol:453](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L453)

## **4. Avoid unused returns**

Having a method that always returns the same value is not correct in terms of consumption, if you want to modify a value, and the method will perform a `revert` in case of failure, it is not necessary to return a `true`, since it will never be `false`. It is less expensive not to return anything, and it also eliminates the need to double-check the returned value by the caller.

```diff
-   function _transferFrom(address from, address to, uint256 amount) internal {
+   function _transferFrom(address from, address to, uint256 amount) internal returns (bool) {
        balanceOf[from] -= amount;

        // Cannot overflow because the sum of all user
        // balances can't exceed the max uint256 value.
        unchecked {
            balanceOf[to] += amount;
        }

        emit Transfer(from, to, amount);

-       return true;
    }
```

### forge test --gas-report

BEFORE in red, AFTER in green:

```diff
    ╭────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
    │ src/Pair.sol:Pair contract ┆                 ┆        ┆        ┆        ┆         │
    ╞════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
    │ Deployment Cost            ┆ Deployment Size ┆        ┆        ┆        ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ 3342647                    ┆ 19392           ┆        ┆        ┆        ┆         │
+   │ 3340439                    ┆ 19381           ┆        ┆        ┆        ┆         │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
    │ Function Name              ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ add                        ┆ 485             ┆ 71816  ┆ 84449  ┆ 110061 ┆ 79      │
+   │ add                        ┆ 485             ┆ 71800  ┆ 84434  ┆ 110042 ┆ 79      │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ buy                        ┆ 458             ┆ 28486  ┆ 32031  ┆ 45624  ┆ 11      │
+   │ buy                        ┆ 458             ┆ 27936  ┆ 32012  ┆ 45605  ┆ 11      │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ nftAdd                     ┆ 103467          ┆ 182464 ┆ 183514 ┆ 233870 ┆ 18      │
+   │ nftAdd                     ┆ 103452          ┆ 182449 ┆ 183499 ┆ 233851 ┆ 18      │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ nftBuy                     ┆ 8988            ┆ 82880  ┆ 101383 ┆ 103383 ┆ 5       │
+   │ nftBuy                     ┆ 8988            ┆ 82866  ┆ 101364 ┆ 103364 ┆ 5       │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ nftRemove                  ┆ 12126           ┆ 94746  ┆ 132800 ┆ 139300 ┆ 6       │
+   │ nftRemove                  ┆ 12126           ┆ 94733  ┆ 132781 ┆ 139281 ┆ 6       │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ nftSell                    ┆ 130248          ┆ 142435 ┆ 144672 ┆ 149922 ┆ 6       │
+   │ nftSell                    ┆ 130229          ┆ 142419 ┆ 144653 ┆ 149903 ┆ 6       │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ remove                     ┆ 13769           ┆ 59863  ┆ 77115  ┆ 83615  ┆ 10      │
+   │ remove                     ┆ 13769           ┆ 54748  ┆ 67220  ┆ 83596  ┆ 10      │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ sell                       ┆ 8613            ┆ 30250  ┆ 36729  ┆ 43229  ┆ 7       │
+   │ sell                       ┆ 8613            ┆ 30235  ┆ 36710  ┆ 43210  ┆ 7       │
    ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-   │ wrap                       ┆ 95561           ┆ 123393 ┆ 143961 ┆ 145187 ┆ 7       │
+   │ wrap                       ┆ 95561           ┆ 123393 ┆ 143961 ┆ 145187 ┆ 7       │
    ╰────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
```

**Affected source code:**

The `_transferFrom` returns never is checked, so it's better to remove it.

- [Pair.sol:447-459](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L447-L459)
