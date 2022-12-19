## [L-01] Use `bytes32` instead of `bytes23` in checking merkleRoot.

There is a typo here in 3rd line.

```solidity
function _validateTokenIds(uint256[] calldata tokenIds, bytes32[][] calldata proofs) internal view {
        // if merkle root is not set then all tokens are valid
        if (merkleRoot == bytes23(0)) return; // [TYPO]

        // validate merkle proofs against merkle root
        for (uint256 i = 0; i < tokenIds.length; i++) {
            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
            require(isValid, "Invalid merkle proof");
        }
    }
```

## [L-02] No transfer ownership pattern.

The current ownership transfer process involves the current owner calling `transferOwnership` . This function just proceeds to write the new owner’s address into the owner variable. If the nominated EOA account is not a valid account (accidently mistyped etc.) , it is entirely possible the owner may accidentally transfer ownership to an uncontrolled account, breaking all functions with the `onlyOwner()` modifier.

Recommend considering implementing a two step process where the owner nominates an account and the nominated account needs to call an `acceptOwnership()` function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

The code would look something like this:

```solidity
    address private ownerCandidate;
    modifier onlyOwnerCandidate() {
        assert(msg.sender == ownerCandidate);
        _;
    }
    function transferOwnership(address candidate) external onlyOwner {
        ownerCandidate = candidate;
    }
    function acceptOwnership() external onlyOwnerCandidate {
        owner = ownerCandidate;
    }
```