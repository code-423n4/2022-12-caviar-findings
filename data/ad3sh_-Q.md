## Use babylonian method to get square root

You can use the [babylonian method](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Babylonian_method) to calculate the square root, since its simpler it will make you save gas.

[2022-12-caviar/Pair.sol at main · code-423n4/2022-12-caviar](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L426)

    `function sqrt(uint x)  internal pure returns (uint y) { unchecked {
        uint z = (x + 1) / 2;
        y = x;
        while (z < y) {
            y = z;
            z = (x / z + z) / 2;
        }
    }}`


