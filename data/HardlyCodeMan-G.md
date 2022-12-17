## 2022-12-caviar
### Gas Report 

---

Two gas optimisations in two areas were found other than the [C4udit](https://gist.github.com/Picodes/42f9144fd8cba738f3a7098411737760) public findings.
- [Owned.sol not functionally implemented](#owned.sol-not-functionally-implemented-in-caviar.sol)
- [Additional to automated Gas-3](#additional-to-auto-audit-finding)

---

### Owned.sol not functionally implemented in Caviar.sol

| Area | Gas Saving  |
|:--|--:|
| Deployment | 60500 |
| owner() | 144 - 2144 |

```Owned.sol``` is only really utilised (through the onlyOwner modifier) in ```LPToken.sol``` and therefore inherited into ```Pair.sol``` via import. There is no actual utilisation of ```Owned.sol``` in ```Caviar.sol``` other than setting the owner variable which is called in ```Pair.sol``` returning ```address(msg.sender)``` from the contract creation. 

By removing ```Owned.sol``` utility from ```Caviar.sol``` and simply using ```address public immutable owner;``` and setting ```owner = msg.sender``` in ```constructor()```, the owner variable is still availbe to ```Pair.sol``` via ```caviar.owner()``` due to the owner variable being public and solidity automatically creatign a getter function for it. There is no code to suggest any change in ownership so the variable can also be set immutable, saving further gas at runtime.

### Pre

[Caviar.sol](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Caviar.sol#L4)
```solidity
import "../lib/solmate/src/auth/Owned.sol";
...
constructor() Owned(msg.sender) {}
```
### Post

```solidity
//import "../lib/solmate/src/auth/Owned.sol";
...
address public immutable owner;
constructor() { owner = msg.sender; }
```

| Area |         |
|:--|--:|
| (Pre) Deployment Cost          | 4625247 |
| (Post) Deployment Cost         | 4564747 |
| Cost Saving                    | 60500   |

| Function Name        | min     | avg     | median  | max     | # calls |
|:--|--:|--:|--:|--:|--:|
| (Pre) owner          | 348     | 1681    | 2348    | 2348    | 12      |
| (Post) owner         | 204     | 204     | 204     | 204     | 12      |
| Gas Diff             | 144     | 1477    | 2144    | 2144    | 0       |

---

### Additional to auto audit finding

- Finding [Gas-3](https://gist.github.com/Picodes/42f9144fd8cba738f3a7098411737760#gas-3-cache-array-length-outside-of-loop)

The automated findings correctly reported tokenIds.length should be cached outside for loops in [Pair.sol](), however additionally to the use of ```tokenIds.length``` in the loops witin ```wrap()``` and ```unwrap()``` there is an additional use to within these functions ```fractionalTokenAmount = tokenIds.length * ONE;``` which should also be referenced by the recommended fix of cached uint for the array length.

### Proposed Additional Fix Example

- [Pair.sol #L217-L243](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L217-L243)
- [Pair.sol #L248-L263](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L248-L263)

```solidity
// *** Effects *** //
uint arrayLen = tokenIds.length; /// @audit Cache array length for multiple references as well as use in the for loop

// mint fractional tokens to sender
fractionalTokenAmount = arrayLen * ONE; /// @audit used cached value
_mint(msg.sender, fractionalTokenAmount);

// *** Interactions *** //

// transfer nfts from sender
for (uint256 i = 0; i < arrayLen; i++) { /// @audit used cached value
    ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
}```