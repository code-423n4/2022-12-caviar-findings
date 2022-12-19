## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the instances with missing NatSpec:

[File: Caviar.sol#L15-L16](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L15-L16)

```
    event Create(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
    event Destroy(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
```
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L20-L52
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L11-L14

## `delete` implication in `destroy()` of `Caviar.sol`
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=delete#delete

`delete` is actually not really deletion at all - it's only assigning a default value back to variables. It does not make difference whether the variable is in memory or in storage. Specifically, it has no effect on whole mappings (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The code line below is merely resetting the selected `pair` to zero address and not destroying anything in essence. Neither will it have any effect on the operation of Pair.sol:

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L53-L54

```
        // delete the pair
        delete pairs[nft][baseToken][merkleRoot];
```
## Rebase/fee-on-transfer tokens
Since rebase and fee-on-transfer tokens are not supported by the AMM, there should be a whitelist logic in [`create()`](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28-L43) of `Caviar.sol` preventing the deployment of any pair that would have `baseToken` associated with them. Otherwise, there will not be any guarantee of not using these tokens to break the swap curve and liquidity maths.

## Stuck tokens/nfts
There is simply no contracts that could prevent this from happening. Consider implementing access controlled withdraw functions to cater for these situations that could be appear as follows:

```
    function withdrawOtherToken(ERC20 otherToken) external {
        require(caviar.owner() == msg.sender, "withdrawOtherToken: not owner");
        require(address(otherToken) != baseToken, "INVALID_TOKEN"); 
        uint256 bal = otherToken.balanceOf(address(this));
        require(bal > 0, "INSUFFICIENT_BALANCE");
        otherToken.safeTransfer(msg.sender, bal);
    } 
```
```
    function withdrawOtherNft(ERC721 _otherNft, uint256 tokenId) external {
        require(caviar.owner() == msg.sender, "withdrawOtherNft: not owner");
        require(address(otherNft) != nft, "INVALID_NFT"); 
        otherNft.safeTransferFrom(address(this), msg.sender, tokenId);
    } 
```
Note: Base tokens and fractional tokens accidentally sent in to Pair.sol would only benefit all existing liquidity providers as the tokens will correspondingly be reflected in `baseTokenReserves()` and `fractionalTokenReserves()`.

## Use `bytes32` instead of `bytes23`
`bytes23` was used in the code line below instead of `bytes32`. Although `merkleRoot` of bytes32(0) might skip the first if block of `_validateTokenIds()`, it will eventually revert in the for loop that follows. Consider refactoring the affected code line as follows so it could end the function logic earlier to avoid unnecessary wastage of gas:

[File: Pair.sol#L465](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L465)

```diff
-        if (merkleRoot == bytes23(0)) return;
+        if (merkleRoot == bytes32(0)) return;
```
## Unspecific compiler version pragma
For most source-units the compiler version pragma is very unspecific ^0.8.17. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instances below could be refactored as follows:

[File: Caviar.sol#L4-L7](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L4-L7)

```
import {Owned} from "solmate/auth/Owned.sol";

import {SafeERC20Namer} from "./lib/SafeERC20Namer.sol";
import {Pair.sol} from "./Pair.sol";
```
## Non-fungible nature of NFTs
It is difficult to maintain a collection of NFTs with the same values considering at some point in the future, some of them are going to be worth more than the others. When this happened, traders would race into cherry picking the `tokenIds` desired when attempting to call `nftBuy()` and/or `unwrap()`.