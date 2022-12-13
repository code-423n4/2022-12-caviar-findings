## Missing validation address

The zero address can be minted in `_mint(...)` function. 

File: [src/LpToken.sol#L19](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19)

```solidity=

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }
```

Similarly, the constructor in `Pair` contract does not validate the address `_nft`. Once this address is set, there is no way to change. 

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

**Recommendation:** Ensure that `to` and `_nft` addresses are not `address(0)`.

---