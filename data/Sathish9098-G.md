
##

## [GAS-1]    instead of using operator && on single require checks . using double REQUIRE checks can save more gas. 
After splitting require statement its possible to save 8 gas

>  BEFORE MIGRATION : 

require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

As per remix execution cost  :  21262


>  Recommended Mitigation Steps

require(baseTokenAmount > 0, "Input token amount is zero");
require(fractionalTokenAmount > 0, "Input token amount is zero");

After Mitigation the execution cost :  21254

> instances (1)

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

    71:  require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

##

## [GAS-2]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

> instances (3)

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)
 
        function remove(uint256 lpTokenAmount, uint256 minBaseTokenOutputAmount, uint256 minFractionalTokenOutputAmount)
        public
        returns (uint256 baseTokenOutputAmount, uint256 fractionalTokenOutputAmount)

       147:   function buy(uint256 outputAmount, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {

       182:   function sell(uint256 inputAmount, uint256 minOutputAmount) public returns (uint256 outputAmount) {

##

## [ GAS-3]  ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() STATEMENT . This saves 30-40 gas

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

        157:   require(inputAmount <= maxInputAmount, "Slippage: amount in");

        168:   uint256 refundAmount = maxInputAmount - inputAmount;

##

## [GAS-4]  ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

> instances (7)

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

       238:   for (uint256 i = 0; i < tokenIds.length; i++) {

       258:   for (uint256 i = 0; i < tokenIds.length; i++) {

      468:    for (uint256 i = 0; i < tokenIds.length; i++) {

[File: 2022-12-caviar/src/lib/SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol)

      13:    for (uint256 j = 0; j < 32; j++) {

      22:    for (uint256 j = 0; j < charCount; j++) {

     33:     for (uint256 i = 32; i < 64; i++) {

     39:    for (uint256 i = 0; i < charCount; i++) {

##

## [GAS-5]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y>  FOR STATE VARIABLES . SAME FOR SUBRACTION OPERATIONS ALSO.
FOR EVERY CALL CAN SAVE 13 GAS 

> instances (2)

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

     448:  balanceOf[from] -= amount;

     453:  balanceOf[to] += amount;

##

## [GAS-6]  CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS. ITS POSSIBLE TO SAVE 10 GAS FOR EVERY ITERATIONS AS PER REMIX REPORTS 

Declare it outside the loop and only use it inside.

> instances (2)

[2022-12-caviar/src/Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol)

(https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L468-L471)

[File: 2022-12-caviar/src/lib/SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol)

   (https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L13-L15)

##

## [GAS-7]  Use uint256 instead uint8 . uint256 consumes less gas than uint8 

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations. 

[File: 2022-12-caviar/src/lib/SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol)

        35:   charCount += uint8(b[i]);

  



 



      

























