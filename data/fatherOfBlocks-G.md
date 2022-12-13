**src/Pair.sol**
- L391/399/408/422/438 - Instead of using the expensive fractionalTokenReserves() function, it's much better to directly query storage: balanceOf[address(this)].

- L282/285/328/331/346/351/421/422/423/469/470 - Instead of creating a variable in memory, you could directly perform the operation within the require.
