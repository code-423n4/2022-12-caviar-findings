##

## [NC-1]  NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

> instances (3) 

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol)

        function remove(uint256 lpTokenAmount, uint256 minBaseTokenOutputAmount, uint256 minFractionalTokenOutputAmount)
        public
        returns (uint256 baseTokenOutputAmount, uint256 fractionalTokenOutputAmount)

        147:   function buy(uint256 outputAmount, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {

        182:   function sell(uint256 inputAmount, uint256 minOutputAmount) public returns (uint256 outputAmount) {

##

## [NC-2] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

There are 1 instances of this issue:

            pragma solidity ^0.8.17;

##

## [NC-3]  CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

It is bad practice to use numbers directly in code without explanation

> instances (3) 

  [File: 2022-12-caviar/src/lib/SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol)

      13:  for (uint256 j = 0; j < 32; j++) {

      33:     for (uint256 i = 32; i < 64; i++) {

      40:   bytesStringTrimmed[i] = b[i + 64];

##

## [L-1] tokenIds, proofs LENGTH SHOULD BE CHECKED BEFORE CALLING _validateTokenIds FUNCTION. THERE IS POSSIBLE CALL FUNCTION WITH DIFFERENT LENGTHS .

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

   (https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L217-L227)

##

## [L-2]  tokenIds length must be checked with require statement. There is the possibility the caller calls functions with empty array . The tokenIds length must be greater than 0.

(https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L248-L263)

Recommended mitigation steps:

require( tokenIds.length >0,"The length is less than zero");








































