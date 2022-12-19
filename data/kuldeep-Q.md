# Informational 1
In `event Withdraw(uint256 tokenId)`, the tokenId can be indexed for better logging. 

## Explanattion
The indexed parameters for logged events will allow you to search for these events using the indexed parameters as filters. Here `Withdraw` event signifies an event when Admin pulls out an NFT for auctioning and this seems an important event to be logged as in which NFT was withdrawn.

## Mitigation
event Withdraw(uint256 indexed tokenId);

# Informational 2
The use of the `_transferFrom` internal method in Pair contracts does not check the return value.

## Explanation
`_transferFrom` internal method returns a bool value. None of the uses of `_transferFrom` either collects or checks the return value which is advised to be a good practice.

## Mitigation
Update ` _transferFrom(address, address, uint)` with `require( _transferFrom(address, address, uint), "Transfer failed")`

# Informational 3
Typo in line#.

## Explanation
`bytes23()` should be `bytes32()`.

