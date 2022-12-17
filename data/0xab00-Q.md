
## bytes23 typo in Pair.sol's _validateTokenIds()

`merkleRoot` is a bytes32. But this check is done:
```
if (merkleRoot == bytes23(0)) return;
 ```
 
This typo of bytes23 (should be bytes32) doesn't have any real impact though.
 
## Skip merkle tree validation 
Documentation about skipping merkle tree validation is only deep within `_validateTokenIds`. I would recommend it is added to `wrap()` and also the docs on their site


The documentation in `create()` (in Caviar) is "merkleRoot The merkle root for the valid tokenIds.". No mention of special case when it is `0`. 

```
     // if merkle root is not set then all tokens are 
     if (merkleRoot == bytes23(0)) return;
```
 
