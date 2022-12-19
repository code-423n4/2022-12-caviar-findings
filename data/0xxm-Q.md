The length of tokenIds and proofs are not checked. In such a case, in the `for` loop of the function `_validateTokenIds`, `proofs[i]` may be out of bounds and cause the transaction to revert without clear reason.

```
function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
    // if merkle root is not set then all tokens are valid
    if (merkleRoot == bytes23(0)) return;

    // validate merkle proofs against merkle root
    for (uint256 i = 0; i < tokenIds.length; i++) {
        bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
        require(isValid, "Invalid merkle proof");
    }
}
```