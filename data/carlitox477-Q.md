# Caviar.create: if contract is meant to be used multichain, ```baseTokenSymbol = "ETH"``` should be changed
For instance, if this contract is ment to be deployed also in polygon, then ```baseTokenSymbol = "ETH"``` would be ok.

It would be advisible to add an immutable variable call ```string immutable NATIVE_CRYPTO_SYMBOL``` and assing the correct value in the constructor. Then replace
```string memory baseTokenSymbol = baseToken == address(0) ? "ETH" : baseToken.tokenSymbol();``` for ```string memory baseTokenSymbol = baseToken == address(0) ? NATIVE_CRYPTO_SYMBOL : baseToken.tokenSymbol();```

# Change ```mapping(address => mapping(address => mapping(bytes32 => address))) public pairs;``` for ```mapping(address => mapping(bytes32 => mapping(address => address))) public pairs;``` would be better.
Current implementation means that associating an NFT with the base token is more important than asociating the NFT with their merkleRoot to identify a pair. However, the merkleRoot can be interpreted as the NFT collection type (for instance: Floor, Mid, Rare or Grail).

For this reason, changin current ```pairs``` type to ```mapping(address => mapping(bytes32 => mapping(address => address)))``` would be better semantically talking. Then next lines hsould be updated:

```solidity
// Caviar.sol
15:     event Create(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
16:     event Destroy(address indexed nft, address indexed baseToken, bytes32 indexed merkleRoot);
19:     mapping(address => mapping(address => mapping(bytes32 => address))) public pairs;
40:     pairs[nft][baseToken][merkleRoot] = address(pair);
42:     emit Create(nft, baseToken, merkleRoot);
51:     require(msg.sender == pairs[nft][baseToken][merkleRoot], "Only pair can destroy itself");
54:     delete pairs[nft][baseToken][merkleRoot];
56:     emit Destroy(nft, baseToken, merkleRoot);
```

