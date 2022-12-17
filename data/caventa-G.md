1. 

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L465

This code should move to the constructor

```
    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
        // if merkle root is not set then all tokens are valid
        --- if (merkleRoot == bytes23(0)) return;

        // validate merkle proofs against merkle root
        for (uint256 i = 0; i < tokenIds.length; i++) {
            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
            require(isValid, "Invalid merkle proof");
        }
    }
```

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L49

```
 constructor(
        address _nft,
        address _baseToken,
        bytes32 _merkleRoot,
        string memory pairSymbol,
        string memory nftName,
        string memory nftSymbol
    ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
        nft = _nft;
        baseToken = _baseToken; // use address(0) for native ETH
        merkleRoot = _merkleRoot;
       +++ if (merkleRoot == bytes23(0)) return;
        lpToken = new LpToken(pairSymbol);
        caviar = Caviar(msg.sender);
    }
```

Checking should be done in the constructor.
It wastes gas if the checking is done every time when _validateTokenIds is called