## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Use `_safeMint` instead of `_mint` | 2 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Contracts are not using their OZ Upgradeable counterparts | 2 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing parameter validation in `constructor` | 3 |

Total: 7 contexts over 3 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Implementation contract may not be initialized | 3 |
| [NC&#x2011;2](#NC&#x2011;2) | Avoid Floating Pragmas: The Version Should Be Locked | 4 |
| [NC&#x2011;3](#NC&#x2011;3) | Use of Block.Timestamp | 1 |
| [NC&#x2011;4](#NC&#x2011;4) | Non-usage of specific imports | 4 |
| [NC&#x2011;5](#NC&#x2011;5) | Use `bytes.concat()` | 1 |
| [NC&#x2011;6](#NC&#x2011;6) | No need to initialize uints to zero | 2 |

Total: 15 contexts over 6 issues

## Low Risk Issues



### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
20: _mint(to, amount);
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L20

```solidity
233: _mint(msg.sender, fractionalTokenAmount);
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L233



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
8: import "openzeppelin/utils/math/Math.sol";

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L8

```solidity
4: import "openzeppelin/utils/Strings.sol";

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L4



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
40: address _nft
41: address _baseToken
42: bytes32 _merkleRoot,

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L40-L42



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



## Non Critical Issues



### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
21: constructor() Owned(msg.sender)
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L21

```solidity
11: constructor(string memory pairSymbol)
        Owned(msg.sender)
        ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L11

```solidity
39: constructor(
        address _nft,
        address _baseToken,
        bytes32 _merkleRoot,
        string memory pairSymbol,
        string memory nftName,
        string memory nftSymbol
    ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18)
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L39





### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

```solidity
Found usage of floating pragmas ^0.8.17 of Solidity in [Caviar.sol]
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L2

```solidity
Found usage of floating pragmas ^0.8.17 of Solidity in [LpToken.sol]
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/LpToken.sol#L2

```solidity
Found usage of floating pragmas ^0.8.17 of Solidity in [Pair.sol]
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L2

```solidity
Found usage of floating pragmas ^0.8.17 of Solidity in [SafeERC20Namer.sol]
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L2






### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
367: require(block.timestamp >= closeTimestamp, "Not withdrawable yet");
```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L367



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
6: import "./lib/SafeERC20Namer.sol";
7: import "./Pair.sol";

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Caviar.sol#L6-L7

```solidity
10: import "./LpToken.sol";
11: import "./Caviar.sol";

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L10-L11




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
469: bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/Pair.sol#L469




#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
12: uint256 charCount = 0;

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L12

```solidity
31: uint256 charCount = 0;

```

https://github.com/code-423n4/2022-12-caviar/tree/main/src/lib\SafeERC20Namer.sol#L31





