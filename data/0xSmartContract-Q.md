## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Missing ReEntrancy Guard to `withdraw` function | 1 |
|[L-02]| Use ```safeTransferOwnership``` instead of ```transferOwnership``` function | 1 |
|[L-03]| Missing Event for critical parameters init and change| 3 |
|[L-04]| Loss of precision due to rounding| 1 |
|[L-05]| Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists| 10 |
|[L-06]| Should an airdrop token arrive on the `pair.sol` contract, it will be stuck | 1 |
|[L-07]| Add to _blacklist function_  | 1 |

Total 7 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Insufficient coverage|1|
| [N-02] |NatSpec comments should be increased in contracts |All Contracts |
| [N-03] |`Function writing` that does not comply with the `Solidity Style Guide`| All Contracts |
| [N-04] |Solidity compiler optimizations can be problematic |  |
| [N-05] |For modern and more readable code; update import usages | 13|
| [N-06] |_Lock pragmas_ to specific compiler version| 5 |
| [N-07] |Use underscores for number literals| 2 |
| [N-08] |Use of bytes.concat() instead of `abi.encodePacked()` | 1 |
| [N-09] |Pragma version^0.8.17  version too recent to be trusted| All Contracts |
| [N-10] |Add EIP-2981 NFT Royalty Standart Support | 1  |

Total 10 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Project Upgrade and Stop Scenario should be |
| [S-02] |Generate perfect code headers every time |

Total 2 suggestions

### [L-01] Missing ReEntrancy Guard to `withdraw` function

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L359-L373

### Impact

Position.sol contract has no Re-Entrancy protection in `withdraw` function

```solidity
src/Pair.sol:

 function withdraw(uint256 tokenId) public {
        // check that the sender is the caviar owner
        require(caviar.owner() == msg.sender, "Withdraw: not owner");

        // check that the close period has been set
        require(closeTimestamp != 0, "Withdraw not initiated");

        // check that the close grace period has passed
        require(block.timestamp >= closeTimestamp, "Not withdrawable yet");

        // transfer the nft to the caviar owner
        ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenId);

        emit Withdraw(tokenId);
    }

```

if the mint was initiated by a contract, then the contract is checked for its ability to receive ERC721 tokens. Without reentrancy guard, onERC721Received will allow an attacker controlled contract to call the mint again, which may not be desirable to some parties, like allowing minting more than allowed.
https://www.paradigm.xyz/2021/08/the-dangers-of-surprising-code




### Proof of Concept
If `withdraw` is msg.sender contract, it can do re-entrancy by overriding `onERC721Received` function, it doesn't seem to be a serious problem since it conforms to check-effect-interaction pattern, but this is a clear re-entry due to access to other functions and pre-emit processing. is the entracy



```solidity
reentrancy.sol:
 function onERC721Received(
    address,
    address,
    uint256,
    bytes memory
  ) public virtual override returns (bytes4) {
    //...do something
    }
    return this.onERC721Received.selector;
  }

```

### Recommended Mitigation Steps
Use Openzeppelin or Solmate Re-Entrancy pattern
Here is a example of a re-entrancy guard

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ReEntrancyGuard {
    bool internal locked;

    modifier noReentrant() {
        require(!locked, "No re-entrancy");
        locked = true;
        _;
        locked = false;
    }
}
```

### [L-02] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
```solidity
2 results - 2 files

src/Caviar.sol:
   4: import "solmate/auth/Owned.sol";
  12: contract Caviar is Owned {


src/LpToken.sol:
   4: import "solmate/auth/Owned.sol";
  11: contract LpToken is Owned, ERC20 {

```

**Description:**
```transferOwnership``` function is used to change Ownership from ```Owned.sol```.

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)

### [L-03] Missing Event for critical parameters init and change

**Context:**
```js
src/Pair.sol:
  38  
  39:     constructor(
  40:         address _nft,
  41:         address _baseToken,
  42:         bytes32 _merkleRoot,
  43:         string memory pairSymbol,
  44:         string memory nftName,
  45:         string memory nftSymbol
  46:     ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
  47:         nft = _nft;
  48:         baseToken = _baseToken; // use address(0) for native ETH
  49:         merkleRoot = _merkleRoot;
  50:         lpToken = new LpToken(pairSymbol);
  51:         caviar = Caviar(msg.sender);
  52:     }

src/LpToken.sol:
  11  contract LpToken is Owned, ERC20 {
  12:     constructor(string memory pairSymbol)
  13:         Owned(msg.sender)
  14:         ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
  15:     {}


src/Pair.sol:
  38  
  39:     constructor(
  40:         address _nft,
  41:         address _baseToken,
  42:         bytes32 _merkleRoot,
  43:         string memory pairSymbol,
  44:         string memory nftName,
  45:         string memory nftSymbol
  46:     ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
  47:         nft = _nft;
  48:         baseToken = _baseToken; // use address(0) for native ETH
  49:         merkleRoot = _merkleRoot;
  50:         lpToken = new LpToken(pairSymbol);
  51:         caviar = Caviar(msg.sender);
  52:     }

```


**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

### [L-04] Loss of precision due to rounding

Add scalars so roundings are negligible


```solidity

src/Pair.sol:
  390:     function price() public view returns (uint256) {
  391:         return (_baseTokenReserves() * ONE) / fractionalTokenReserves();
  392:     }

```
### [L-05] Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists

Solmate's SafeTransferLib, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist (yet).

This is stated in the Solmate library:
https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9



```solidity

10 results - 1 file

src/Pair.sol:
   94              // transfer base tokens in
   95:             ERC20(baseToken).safeTransferFrom(msg.sender, address(this), baseTokenAmount);
   96          }

  133              // if base token is native ETH then send ether to sender
  134:             msg.sender.safeTransferETH(baseTokenOutputAmount);
  135          } else {
  136              // transfer base tokens to sender
  137:             ERC20(baseToken).safeTransfer(msg.sender, baseTokenOutputAmount);
  138          }

  168              uint256 refundAmount = maxInputAmount - inputAmount;
  169:             if (refundAmount > 0) msg.sender.safeTransferETH(refundAmount);
  170          } else {
  171              // transfer base tokens in
  172:             ERC20(baseToken).safeTransferFrom(msg.sender, address(this), inputAmount);
  173          }

  199              // transfer ether out
  200:             msg.sender.safeTransferETH(outputAmount);
  201          } else {
  202              // transfer base tokens out
  203:             ERC20(baseToken).safeTransfer(msg.sender, outputAmount);
  204          }

  238          for (uint256 i = 0; i < tokenIds.length; i++) {
  239:             ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
  240          }

  258          for (uint256 i = 0; i < tokenIds.length; i++) {
  259:             ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
  260          }

  369          // transfer the nft to the caviar owner
  370:         ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenId);
  371 
```


### Recommended Mitigation Steps

Add a contract exist control in functions;
```js
pragma solidity >=0.8.0;

function isContract(address _addr) private returns (bool isContract) {
    isContract = _addr.code.length > 0;
}

```
### [L-06] Should an airdrop token arrive on the `pair.sol` contract, it will be stuck

With the `wrap()` function, NFTs are transferred to the contract and in case of airdrop due to these NFTs, it will be stuck in the contract as there is no function to take these airdrop tokens from the contract.

Important NFT project owners are given airdrops, especially since the project includes NFTs such as BAYC, Moonbirds, Doodles, Azuki, there is a high probability of receiving Airdrops, but there is no function to withdraw incoming airdrop tokens, so airdrop tokens will be stuck in the contract.

A common method for airdrops is to collect airdrops with `claim`, so the `Pair.sol` contract can be considered upgradagable, adding a function to make `claim`.



```solidity
src/Pair.sol:
  216      /// @return fractionalTokenAmount The amount of fractional tokens minted.
  217:     function wrap(uint256[] calldata tokenIds, bytes32[][] calldata proofs)
  218:         public
  219:         returns (uint256 fractionalTokenAmount)
  220:     {
  221:         // *** Checks *** //
  222: 
  223:         // check that wrapping is not closed
  224:         require(closeTimestamp == 0, "Wrap: closed");
  225: 
  226:         // check the tokens exist in the merkle root
  227:         _validateTokenIds(tokenIds, proofs);
  228: 
  229:         // *** Effects *** //
  230: 
  231:         // mint fractional tokens to sender
  232:         fractionalTokenAmount = tokenIds.length * ONE;
  233:         _mint(msg.sender, fractionalTokenAmount);
  234: 
  235:         // *** Interactions *** //
  236: 
  237:         // transfer nfts from sender
  238:         for (uint256 i = 0; i < tokenIds.length; i++) {
  239:             ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
  240:         }
  241: 
  242:         emit Wrap(tokenIds);
  243:     }

```


### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```

## [L-07] Add to _blacklist function_

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity.
To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.


Here is the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps :
Add to Blacklist function and modifier.
### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is 97%. Testing all functions is best practice in terms of security criteria.

```js

| File                                     | % Lines          | % Statements      | % Branches     | % Funcs        |
|------------------------------------------|------------------|-------------------|----------------|----------------|
| src/Caviar.sol                           | 100.00% (11/11)  | 100.00% (15/15)   | 100.00% (4/4)  | 100.00% (2/2)  |
| src/LpToken.sol                          | 100.00% (2/2)    | 100.00% (2/2)     | 100.00% (0/0)  | 100.00% (2/2)  |
| src/Pair.sol                             | 100.00% (88/88)  | 100.00% (107/107) | 95.24% (40/42) | 86.36% (19/22) |
| src/lib/SafeERC20Namer.sol               | 0.00% (0/38)     | 0.00% (0/53)      | 0.00% (0/12)   | 0.00% (0/7)    |

```
Due to its capacity, test coverage is expected to be 100%.

### [N-02] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts
### [N-03] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-04] Solidity compiler optimizations can be problematic

```js

foundry.toml:
  1: [profile.default]
  2: src = "src"
  3: out = "out"
  4: libs = ["lib"]
  5: solc = "0.8.17"
  6: optimizer_runs = 3_000

```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [N-05] For modern and more readable code; update import usages

**Context:**

```solidity

13 results - 4 files

src/Caviar.sol:
  3  
  4: import "solmate/auth/Owned.sol";
  5  
  6: import "./lib/SafeERC20Namer.sol";
  7: import "./Pair.sol";
  8  

src/LpToken.sol:
  3  
  4: import "solmate/auth/Owned.sol";
  5: import "solmate/tokens/ERC20.sol";
  6  

src/Pair.sol:
   3  
   4: import "solmate/tokens/ERC20.sol";
   5: import "solmate/tokens/ERC721.sol";
   6: import "solmate/utils/MerkleProofLib.sol";
   7: import "solmate/utils/SafeTransferLib.sol";
   8: import "openzeppelin/utils/math/Math.sol";
   9  
  10: import "./LpToken.sol";
  11: import "./Caviar.sol";
  12  

src/lib/SafeERC20Namer.sol:
  3  
  4: import "openzeppelin/utils/Strings.sol";
  5 

```


**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-06] _Lock pragmas_ to specific compiler version

**Description:**
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally. 
https://swcregistry.io/docs/SWC-103

**Recommendation:**
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
[solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

```solidity
5 results - 4 files

src/Caviar.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.17;
  3  

src/LpToken.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.17;
  3  

src/Pair.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.17;
  3  

src/lib/SafeERC20Namer.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.17;

```
### [N-07] Use underscores for number literals

```solidity
2 results - 1 file

src/Pair.sol:
  399:         return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);

  413:         return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);


```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-08] Use of bytes.concat() instead of abi.encodePacked()

```solidity

1 result - 1 file

src/Pair.sol:
  473          for (uint256 i = 0; i < tokenIds.length; i++) {
  474:             bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));

```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [N-09] Pragma version^0.8.17  version too recent to be trusted.

https://github.com/ethereum/solidity/blob/develop/Changelog.md
0.8.17 (2022-09-08)
0.8.16 (2022-08-08)
0.8.15 (2022-06-15)
0.8.10 (2021-11-09)


Unexpected bugs can be reported in recent versions;
Risks related to recent releases
Risks of complex code generation changes
Risks of new language features
Risks of known bugs

Use a non-legacy and more battle-tested version
Use 0.8.10


### [N-10] Add EIP-2981 NFT Royalty Standart Support

Consider adding EIP-2981 NFT Royalty Standard to the project

https://eips.ethereum.org/EIPS/eip-2981


Royalty (Copyright – EIP 2981):

Fixed % royalties: For example, 6% of all sales go back to artists
Declining royalties: There may be a continuous decline in sales based on time or any other variable.
Dynamic royalties : Varies over time or sales amount
Upgradeable royalties: Allows a legal entity or NFT owner to change any copyright
Incremental royalties: No royalties, for example when sold for less than $100
Managed royalties : Funds are owned by a DAO, imagine the recipient is a DAO treasury
Royalties to different people : Collectors and artists can even use royalties, not specific to a particular personality

### [S-01] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

### [S-02] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
