---------------------------------------------------------------------------------------------------------------------------------------------------------------
ISSUE #1
Code with Issue : https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L407

Here we have hard coded fees as 30bps. This is not ideal as this does not give the flexibility of increasing/decreasing this fees. Owner(Pool creator) might want to decrease the fees to make it more lucrative for traders to trade in his pool. Or he might want to increase fees given the rarity of his NFTs.

I would recommend a immutable fees variables set by the pool owner at the time of creating pool. This way owner will have the flexibility of controlling his pool and the fees also cannot be changed later giving assurance to traders of his pool.

---------------------------------------------------------------------------------------------------------------------------------------------------------------

ISSUE #2
Global variable msg.sender has type address. Compiler cannot determine whether or not these addresses are payable or not, so it now requires an explicit conversion to make this requirement visible.
As per the breaking changes in v8.0 being mentioned in https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html, It has been recommended to use payable(msg.sender).transfer(x) instead of msg.sender.transfer(x).

In contract Pair.sol we have three instance where this code change can be brought in. These are at :
1) https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L134
2) https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L169
3) https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L200