**src/libs/SafeERC20Namer.sol**
- L77/88 - There is a commented code that does not add anything to the contract, therefore it should be removed.


**src/Pair.sol**
- L16 - The ERC721TokenReceiver inheritance is used, but the import is not found, therefore the code is not found.

- L391/399/40/421/422/437/438 - When we are performing divisions, it should be validated before it is not divided by zero, in order to throw a correct exception and that the user has a correct indication of the reason for reverting.

