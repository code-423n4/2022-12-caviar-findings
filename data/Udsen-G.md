## 1. ++I COSTS LESS GAS COMPARED TO I++ AND INCREMENTS/DECREMENTS CAN BE UNCHECKED IN FOR-LOOPS IF THEY DONOT UNDERFLOW OR OVERFLOW


       for (uint256 i = 0; i < tokenIds.length; i++) {

This can be re written as follows to save gas:

      for (uint256 i = 0; i < tokenIds.length;) {
       unchecked{ ++i;}
      }

There are seven instances of this issue.

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#238

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L468

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L22

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L33

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L39

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L17

## 2. USING ***PRIVATE*** RATHER THAN ***PUBLIC*** FOR CONSTANTS, SAVES GAS

 Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

There are two instances of this issue in Pair.sol

    uint256 public constant ONE = 1e18;
    uint256 public constant CLOSE_GRACE_PERIOD = 7 days;

This can be re-written as 

    uint256 private constant ONE = 1e18;
    uint256 private constant CLOSE_GRACE_PERIOD = 7 days;

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L20-L21

## 3. > 0 IS LESS EFFICIENT THAN != 0 FOR UNSIGNED INTEGERS

        require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

This can be re-written as follows to save gas:

        require(baseTokenAmount != 0 && fractionalTokenAmount != 0, "Input token amount is zero");

There are three instances of this issue in Pair.sol

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L169

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L419
