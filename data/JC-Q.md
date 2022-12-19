
---

## Summary

- N-01 Typos | 1 instance
- N-02 Large multiples of ten should use scientific notation | 2 instance
- N-03 Consider supporting CryptoPunks and EtherRocks | 1 instances
- N-04 The visibility for constructor is ignored | 3 instances

- L-01 Unspecific Compiler Version Pragma | 3 instances
- L-02 Check that Contract Exists before using solmate's SafeTransferLib | 7 instances
- L-03 Pairs do not implement ERC721TokenReceiver | 1 instance


Total: 18 instances in 7 issues.

---

## N-01 Typos

Instance (1):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L465
bytes32 instead of bytes23
 

 
## N-02 Large multiples of ten should use scientific notation  

Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability

Instances (2):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L399
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L408


## N-03 Consider supporting CryptoPunks and EtherRocks

The project SPECIFICATION.md says that Pairs are specifically ERC721 tokens (see import ERC721 lib from solmate), but not all NFTs are ERC721s. 
CryptoPunks and EtherRocks came before the standard and do not conform to it.

Instance (1):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L5
 

## N-04 The visibility for constructor is ignored

Instances (3):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L39

Caviar.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L21

LpToken.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L11



## L-01 Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts. 
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. 
A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. 
It is recommended to pin to a concrete compiler version, e.g. 'pragma solidity ^0.8.0;' -> 'pragma solidity 0.8.4;"

Instances (3):

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L2
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L2
https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L2



## L-02 Check that Contract Exists before using solmate's SafeTransferLib

Functions in solmate's SafeTransferLib library do not check whether a token has code at all. This responsibility is delegated to the caller.
As a call to an address with no code will be a no-op, since low-level calls to non-contracts always return true, a transfer of tokens using solmate's SafeTransferLib will succeed if the token does not have any code.
Therefore, it is recommended to verify that a contract exists before using any SafeTransferLib functions.

Instances (7):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L95
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L137
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L172
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L203
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L239
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L259
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L370



## L-03 Pairs do not implement ERC721TokenReceiver

According to the SPECIFICATION.md, Pairs specifically involve ERC721 tokens. 
Therefore the contract should implement ERC721TokenReceiver, or customer transfers involving safeTransferFrom() calls will revert.

Instance (1):

Pair.sol
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L5


