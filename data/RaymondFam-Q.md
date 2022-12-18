## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the instances with missing NatSpec:

[File: Caviar.sol#L15-L16](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L15-L16)

```
    event Create(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
    event Destroy(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
```
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
