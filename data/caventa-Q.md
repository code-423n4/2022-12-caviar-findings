1. 

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L33-L37

```
     string memory baseTokenSymbol = baseToken == address(0) ? "ETH" : baseToken.tokenSymbol();
     string memory nftSymbol = nft.tokenSymbol();
     string memory nftName = nft.tokenName();
     string memory pairSymbol = string.concat(nftSymbol, ":", baseTokenSymbol);
     pair = new Pair(nft, baseToken, merkleRoot, pairSymbol, nftName, nftSymbol);
```

"ETH" is used as an identifier for Pair to receive the native token Ether. 
However, If the ERC20 token has "ETH" as symbol, this will be confusing.
Consider modifying the Pair constructor to accept a boolean flag to determine if the native token is used in the pair contract.

2.

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L95
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L134
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L137
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L169
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L172
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L200
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L203
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L239
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L259
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L370

It is recommended that don't use solmate library to do transfers.

The safetransfer and safetransferfrom doesn’t check the existence of code at the token address. This is a known issue while using solmate’s libraries. Hence this may lead to miscalculation of funds and may lead to loss of funds , because if safetransfer() and safetransferfrom() are called on a token address that doesn’t have contract in it, it will always return success, bypassing the return value check. 

We should use Use openzeppelin’s safeERC20 or implement a code existence check before every safeTransfer and safeTransferFrom.

3. Unwrap function should check the tokens exist in the merkle root like in the wrap function

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L248-L263

```
   ---   function unwrap(uint256[] calldata tokenIds) public returns (uint256 fractionalTokenAmount) {
   +++ function unwrap(uint256[] calldata tokenIds, bytes32[][] calldata proofs) public returns (uint256 fractionalTokenAmount) {
   +++ _validateTokenIds(tokenIds, proofs); 

       // *** Effects *** //

        // burn fractional tokens from sender
        fractionalTokenAmount = tokenIds.length * ONE;
        _burn(msg.sender, fractionalTokenAmount);

        // *** Interactions *** //

        // transfer nfts to sender
        for (uint256 i = 0; i < tokenIds.length; i++) {
            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
        }

        emit Unwrap(tokenIds);
    }
```

4.  We should ensure the length of tokenIds and proofs are the same.

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L463-L472

```
    function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
    +++ require(tokenIds.length == proofs.length, "Length should be the same");
        // if merkle root is not set then all tokens are valid
        if (merkleRoot == bytes23(0)) return;

        // validate merkle proofs against merkle root
        for (uint256 i = 0; i < tokenIds.length; i++) {
            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
            require(isValid, "Invalid merkle proof");
        }
    }
```