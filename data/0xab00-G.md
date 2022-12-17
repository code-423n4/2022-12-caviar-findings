
## Adding indexed to events can reduce gas cost

The emitted events have no indexed params. Using indexed params can reduce gas cost. 

Demo from Pair.sol

```
pragma solidity >=0.8.4;

contract AddingIndexedToEvents {


    event Add(uint256 indexed baseTokenAmount,  uint256 indexed fractionalTokenAmount, uint256 indexed lpTokenAmount);
    event Remove(uint256 indexed baseTokenAmount, uint256 indexed fractionalTokenAmount, uint256 indexed lpTokenAmount);
    event Buy(uint256 indexed inputAmount, uint256 indexed outputAmount);
    event Sell(uint256 indexed inputAmount, uint256 indexed outputAmount);
    event Wrap(uint256[] indexed tokenIds);
    event Unwrap(uint256[] indexed tokenIds);
    event Close(uint256 indexed closeTimestamp);
    event Withdraw(uint256 indexed tokenId);

    uint256[] tokenIds = [1234,1234];
    // Add() unoptimized 26,708 gas
    // Add() optimized 26,562 gas (146 cheaper)

    // Remove() unoptimized 26,708 gas
    // Remove() optimized 26,562 gas (146 cheaper) 

    // Buy() unoptimized 26,246 gas
    // Buy() optimized 26,127 gas (119 cheaper)

    // Sell() unoptimized 26,246 gas
    // Sell() optimized  26,127 gas (119 cheaper)

    // Wrap() unoptimized 35,164 gas (note: my demo using storage access)
    // Wrap() optimized 34,438 gas (726 cheaper - suspect I have a bug when testing this )

    // Unwrap() unoptimized 35,164 gas (note: also using storage access)
    // Unwrap() optimized 34,438 gas

    // Close() unoptimized 25,783 gas
    // Close() optimized 25,693 gas (90 cheaper)

    // Withdraw() unoptimized 25,783 gas
    // Close() optimized 25,693 gas (90 cheaper)
    function emitter() public {
        // emit Add(1234,1234,1234);
        // emit Remove(1234,1234,1234);
        // emit Buy(1234,1234);
        // emit Wrap(tokenIds);
        // emit Unwrap(tokenIds);
        // emit Close(1234);
        // emit Withdraw(1234);
    }
}
```

Note: no difference to deploy cost.

Caviar.sol does use indexed params.


-------------

## Pair.sol wrap() - could optimize the loop increment

Can use unchecked in the for loop for slightly less readable but more gas optimized.

Here is a slimmed down version showing the lower gas

```
     function wrap(uint256[] calldata tokenIds)
        public
        returns (uint256 fractionalTokenAmount)
    {

        // unoptimized loop (5 items) 27,131 gas
        for (uint256 i = 0; i < tokenIds.length; i++) {
        }

        // optimized loop (5 items) 26,468 gas (663 gas saving)
        require(tokenIds.length < 2**256 - 1);
        for(uint256 i = 0; i < tokenIds.length;) {
            unchecked {
                i++;
            }
        }
    }
```

This can also be applied to Pair.sol's unwrap() and _validateTokenIds() but I won't list it here as the examples are easy to imagine.


-------------

## Pair.sol wrap() - can optimize by caching tokenIds.length

Assuming a length of 5, this can reduce gas

Example (slimmed down example for demo):
```
     function wrap(uint256[] calldata tokenIds)
        public
        returns (uint256 fractionalTokenAmount)
    {

        // unoptimized loop (5 items) 27,131 gas
        // for (uint256 i = 0; i < tokenIds.length; i++) {
        // }

        // optimized 27,091 gas (5 items in tokenIds) = 40 gas saving
        uint256 cachedLength = tokenIds.length;
        for (uint256 i = 0; i < cachedLength; i++) {
        }
    }
```

This can also be applied to Pair.sol's unwrap() and _validateTokenIds() but I won't list it here as the examples are easy to imagine.

------------

## Pair.sol add() - can optimize by caching a boolean

There are a couple of comparisons of the `baseToken` to `address(0)`. If we cache this, it can save gas.

Here is a simplified example with gas amounts:

```
   function add(uint256 baseTokenAmount)
        public
        payable
    {
        // unoptimized - 251,026 gas to deploy
        // and 24,909 gas to call this function
        // require(baseToken == address(0) ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");

        // if (baseToken != address(0)) {
        //     // do something
        // }

        // ---------------------

        // optimized - 237,907 gas to deploy (13,119 gas saving)
        // and 24,906 gas to call this function (6 gas saving)
        // bool isEth = baseToken == address(0);
        // require(isEth ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");

        // if (!isEth) {
            // do something
        // }

    }
```

This optimizatio can also be applied to remove(), buy(), sell() in very similar ways so i won't list them here as they are easy to imagine.

--------------

## Pair.sol add() - can optimize function execution by splitting up require()

This is unlikely to be useful due to extra deploy fee and minor function execution gas saving, but the optimization is there

Here is a simplified example, showing a more optimized way costs more gas to deploy, but saves 9 gas on each function call. 
```
    function add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 minLpTokenAmount)
        public
        payable
    {
        // unoptimized - costs 176,862 gas to deploy, 25,619 gas to execute this function
        // require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

        // -------------

        // optimized - costs 216,889 gas to deploy (more expensive) but 25610 gas to run (minor saving of 9 gas)
        // require(baseTokenAmount > 0 , "baseTokenAmount is zero");
        // require(fractionalTokenAmount > 0, "fractionalTokenAmount is zero");

    }
 ```

## Pair.sol - can optimize close() by caching data in memory, instead of writing then reading from storage

This is an unlikely good candidate to actually implement, but its mildly interesting. 

Can save 100 gas, but it has larger deployment cost. As this is for an emergency, used a maximum of once, I cannot really recommend this one.

Simplified proof of concept with gas of each.
```
contract Pair  {
    uint256 public closeTimestamp;
    event Close(uint256 closeTimestamp);
    uint256 public constant CLOSE_GRACE_PERIOD = 7 days;

    // unoptimized - costs 169542 to deploy this contract, and 51520 to run the function
    // function close() public {
    //     closeTimestamp = block.timestamp + CLOSE_GRACE_PERIOD;
    //     emit Close(closeTimestamp);
    // }

    // optimized - costs 170,535 to deploy this contract (more expensive), and 51420 to run the function (100 gas saving)
    // function close() public {
    //     uint256 tmpCloseTimestamp = block.timestamp + CLOSE_GRACE_PERIOD;
    //     closeTimestamp = tmpCloseTimestamp;
    //     emit Close(tmpCloseTimestamp);
    // }

}
```

A similar idea can be used with `withdraw()` too (caching `closeTimestamp`) but again due to it being for emeregency exit it is not worth it.
