## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `_safeMint()` should be used rather than `_mint()` wherever possible | 1 | 

Total: 1 instances over 1 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Import declarations should import specific identifiers, rather than the whole file | 13 | 
| [N&#x2011;02] | Contract implements interface without extending the interface | 1 | 
| [N&#x2011;03] | `constant`s should be defined rather than using magic numbers | 16 | 
| [N&#x2011;04] | Non-library/interface files should use fixed compiler versions, not floating ones | 3 | 
| [N&#x2011;05] | File is missing NatSpec | 1 | 
| [N&#x2011;06] | Large or complicated code bases should implement fuzzing tests | 1 | 

Total: 35 instances over 6 issues





## Low Risk Issues

### [L&#x2011;01]  `_safeMint()` should be used rather than `_mint()` wherever possible
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function

*There is 1 instance of this issue:*

```solidity
File: src/Pair.sol

233:          _mint(msg.sender, fractionalTokenAmount);

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L233

## Non-critical Issues

### [N&#x2011;01]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 13 instances of this issue:*

```solidity
File: src/Caviar.sol

4:    import "solmate/auth/Owned.sol";

6:    import "./lib/SafeERC20Namer.sol";

7:    import "./Pair.sol";

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Caviar.sol#L4

```solidity
File: src/lib/SafeERC20Namer.sol

4:    import "openzeppelin/utils/Strings.sol";

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/lib/SafeERC20Namer.sol#L4

```solidity
File: src/LpToken.sol

4:    import "solmate/auth/Owned.sol";

5:    import "solmate/tokens/ERC20.sol";

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/LpToken.sol#L4

```solidity
File: src/Pair.sol

4:    import "solmate/tokens/ERC20.sol";

5:    import "solmate/tokens/ERC721.sol";

6:    import "solmate/utils/MerkleProofLib.sol";

7:    import "solmate/utils/SafeTransferLib.sol";

8:    import "openzeppelin/utils/math/Math.sol";

10:   import "./LpToken.sol";

11:   import "./Caviar.sol";

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L4

### [N&#x2011;02]  Contract implements interface without extending the interface
Not extending the interface may lead to the wrong function signature being used, leading to unexpected behavior. If the interface is in fact being implemented, use the `override` keyword to indicate that fact

*There is 1 instance of this issue:*

```solidity
File: src/LpToken.sol

/// @audit IFutureYieldToken.mint()
10:   contract LpToken is Owned, ERC20 {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/LpToken.sol#L10

### [N&#x2011;03]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 16 instances of this issue:*

```solidity
File: src/lib/SafeERC20Namer.sol

/// @audit 32
11:           bytes memory bytesString = new bytes(32);

/// @audit 32
13:           for (uint256 j = 0; j < 32; j++) {

/// @audit 32
/// @audit 64
33:           for (uint256 i = 32; i < 64; i++) {

/// @audit 8
34:               charCount <<= 8;

/// @audit 64
40:               bytesStringTrimmed[i] = b[i + 64];

/// @audit 32
66:           if (data.length == 32) {

/// @audit 64
69:           } else if (data.length > 64) {

/// @audit 0x95d89b41
78:           string memory symbol = callAndParseStringReturn(token, 0x95d89b41);

/// @audit 0x06fdde03
89:           string memory name = callAndParseStringReturn(token, 0x06fdde03);

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/lib/SafeERC20Namer.sol#L11

```solidity
File: src/LpToken.sol

/// @audit 18
13:           ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/LpToken.sol#L13

```solidity
File: src/Pair.sol

/// @audit 18
46:       ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {

/// @audit 1000
/// @audit 997
399:          return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);

/// @audit 997
407:          uint256 inputAmountWithFee = inputAmount * 997;

/// @audit 1000
408:          return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L46

### [N&#x2011;04]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 3 instances of this issue:*

```solidity
File: src/Caviar.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Caviar.sol#L2

```solidity
File: src/LpToken.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/LpToken.sol#L2

```solidity
File: src/Pair.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L2

### [N&#x2011;05]  File is missing NatSpec

*There is 1 instance of this issue:*

```solidity
File: src/lib/SafeERC20Namer.sol


```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/lib/SafeERC20Namer.sol

### [N&#x2011;06]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: src/Caviar.sol


```


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 2 | 

Total: 2 instances over 1 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `public` functions not called by the contract should be declared `external` instead | 11 | 
| [N&#x2011;02] | `constant`s should be defined rather than using magic numbers | 3 | 
| [N&#x2011;03] | Event is missing `indexed` fields | 8 | 

Total: 22 instances over 3 issues





## Low Risk Issues

### [L&#x2011;01]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 2 instances of this issue:*

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
47:           nft = _nft;

/// @audit (valid but excluded finding)
48:           baseToken = _baseToken; // use address(0) for native ETH

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L47

## Non-critical Issues

### [N&#x2011;01]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 11 instances of this issue:*

```solidity
File: src/Caviar.sol

/// @audit (valid but excluded finding)
28:       function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {

/// @audit (valid but excluded finding)
49:       function destroy(address nft, address baseToken, bytes32 merkleRoot) public {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Caviar.sol#L28

```solidity
File: src/LpToken.sol

/// @audit (valid but excluded finding)
19:       function mint(address to, uint256 amount) public onlyOwner {

/// @audit (valid but excluded finding)
26:       function burn(address from, uint256 amount) public onlyOwner {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/LpToken.sol#L19

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
275       function nftAdd(
276           uint256 baseTokenAmount,
277           uint256[] calldata tokenIds,
278           uint256 minLpTokenAmount,
279           bytes32[][] calldata proofs
280:      ) public payable returns (uint256 lpTokenAmount) {

/// @audit (valid but excluded finding)
294       function nftRemove(uint256 lpTokenAmount, uint256 minBaseTokenOutputAmount, uint256[] calldata tokenIds)
295           public
296:          returns (uint256 baseTokenOutputAmount, uint256 fractionalTokenOutputAmount)

/// @audit (valid but excluded finding)
310:      function nftBuy(uint256[] calldata tokenIds, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {

/// @audit (valid but excluded finding)
323       function nftSell(uint256[] calldata tokenIds, uint256 minOutputAmount, bytes32[][] calldata proofs)
324           public
325:          returns (uint256 outputAmount)

/// @audit (valid but excluded finding)
341       function close() public {
342           // check that the sender is the caviar owner
343:          require(caviar.owner() == msg.sender, "Close: not owner");

/// @audit (valid but excluded finding)
359:      function withdraw(uint256 tokenId) public {

/// @audit (valid but excluded finding)
390:      function price() public view returns (uint256) {

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L275-L280

### [N&#x2011;02]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 3 instances of this issue:*

```solidity
File: src/lib/SafeERC20Namer.sol

/// @audit 160 - (valid but excluded finding)
/// @audit 4 - (valid but excluded finding)
/// @audit 4 - (valid but excluded finding)
55:           return Strings.toHexString(uint160(token) >> (160 - 4 * 4));

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/lib/SafeERC20Namer.sol#L55

### [N&#x2011;03]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 8 instances of this issue:*

```solidity
File: src/Pair.sol

/// @audit (valid but excluded finding)
30:       event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);

/// @audit (valid but excluded finding)
31:       event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);

/// @audit (valid but excluded finding)
32:       event Buy(uint256 inputAmount, uint256 outputAmount);

/// @audit (valid but excluded finding)
33:       event Sell(uint256 inputAmount, uint256 outputAmount);

/// @audit (valid but excluded finding)
34:       event Wrap(uint256[] tokenIds);

/// @audit (valid but excluded finding)
35:       event Unwrap(uint256[] tokenIds);

/// @audit (valid but excluded finding)
36:       event Close(uint256 closeTimestamp);

/// @audit (valid but excluded finding)
37:       event Withdraw(uint256 tokenId);

```
https://github.com/code-423n4/2022-12-caviar/blob/b4b32c049ae71178e3bf5125a3b87a4eebb866f3/src/Pair.sol#L30
