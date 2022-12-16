## Missing validation address

The constructor in `Pair` contract does not validate the address `_nft`. This variable could be set as `address(0)` as mistake. Once this address is set, there is no way to change. 

File: [src/Pair.sol#L39](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L39)

```solidity=
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
        lpToken = new LpToken(pairSymbol);
        caviar = Caviar(msg.sender);
    }
```

**Recommendation:** Ensure that `_nft` address is not `address(0)`.
