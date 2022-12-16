1 Gas optimization issue in the for loop of lines:
https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L238-L240
The increment of i should be marked as unchecked instead of incrementing it the way it is done. Just left a snippet below:

Solution to https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L238-L240:

BEFORE:
for (uint256 i = 0; i < tokenIds.length; i++) {
            ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
        }

AFTER:
for (uint256 i = 0; i < tokenIds.length; ) {
    ERC721(nft).safeTransferFrom(msg.sender, address(this), tokenIds[i]);
    unchecked {   ++ i ; }        
}

2 Same issue in lines: https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L258-L260
use unchecked in the for loop, just like this:
BEFORE:
 for (uint256 i = 0; i < tokenIds.length; i++) {
            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
        }

AFTER:
 for (uint256 i = 0; i < tokenIds.length;) {
            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
unchecked {   ++ i ; }      
        }

3 SAME USECASE for lines https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L468-L471

Add unchecked in the for loop

BEFORE:
for (uint256 i = 0; i < tokenIds.length; ) {
            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
            require(isValid, "Invalid merkle proof");
unchecked {   ++ i ; }    
        }

AFTER:
for (uint256 i = 0; i < tokenIds.length; ) {
            bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
            require(isValid, "Invalid merkle proof");
unchecked {   ++ i ; }    
        }


4 Gas saving using onlyOwner modifier instead of adding the same require statement in different functions.
Lines: 
-https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L343
-https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L361

Take out this lines and add a modifier in the beginning of the contract:

modifier onlyOwner(){
require(owner == msg.sender, "not owner");
_;
}

Initialize the variable owner in the constructor and add this modifier to both functions instead of the requirement statement listed on the above links

5 Optimizing two operations in lines https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L448-L454

It is cheaper to do  balanceOf[from] = balanceOf[from] - amount;  than balanceOf[from] -= amount; in line https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L448
the same in line: https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L453
before: balanceOf[to] += amount;  after: balanceOf[to] = balanceOf[to] + amount;