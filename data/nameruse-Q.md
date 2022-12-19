### Summary

Overall the codebase is quite readable, well organized. The codebase closley matched the documenation specification. Project structure is intuitive and simple to follow. Contract structure is well ogranized and makes good use of libraries and external contracts. Variable naming is easily readble and intuitive. Comments were used rigourously and were beneficial and accurate in describing the object they were comenting on. Events are used in a benefical manner to track protocol outputs.
   


### Non-Critical issues
#### [NC-01] Use named import instead of plain imports

https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

instances:4

File: Pair.sol
Line: 4-8
[Link](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L4-L8)


File: Caviar.sol
Line: 4-7
[Link](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Caviar.sol#L4-L7)

File: LpToken.sol
Line: 4-5
[Link](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/LpToken.sol#L4-L5)

File: SafeERC20Namer.sol
Line: 4
[Link](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L4)



#### [NC-02] Missing @param comments

Function missing @param comments, while function arguments are intuitive @param comments makes code more readable

instances:1

File: Pair.sol
Line: 38-46
[Link](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L38-L46)

```javascript
   constructor(
        address _nft,
        address _baseToken,
        bytes32 _merkleRoot,
        string memory pairSymbol,
        string memory nftName,
        string memory nftSymbol
    ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
```
