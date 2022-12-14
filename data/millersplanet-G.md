## [GAS] Redundant built-in safe arithmetics

Instances (4) : ``SafeERC20Namer.sol#13,22,33,39``

Avoid unnecessary built-in safe arithmetics. In the following `for` loops, each increment is performed with bound-checks when they are entirely redundant :

SafeERC20Namer.sol#13:
```
for (uint256 j = 0; j < 32; j++) {
	bytes1 char = x[j];
	if (char != 0) {
		bytesString[charCount] = char;
		charCount++;
	}
}
```
SafeERC20Namer.sol#22:
```
for (uint256 j = 0; j < charCount; j++) {
        (...)
}
```
SafeERC20Namer.sol#33:
```
for (uint256 i = 32; i < 64; i++) {
        (...)
}
```
SafeERC20Namer.sol#39:
```
for (uint256 i = 0; i < charCount; i++) {
        (...)
}
```

Due to the inherent constraints of Solidity and the declaration of the variables, ``i``  and ``j`` are guaranteed to fit within a ``uint256`` variable, meaning that performing a safe incrementation on each loop for the `i` and `j` variables are redundant and costly.

To optimize such code blocks, it's recommended to place the code implementation of the `bytes32ToString` and the `parseStringData` functions inside an ``unchecked`` code block, saving, for example, around 8000 gas in a 13 character string conversion:

SafeERC20Namer.sol#10-27:
```
function bytes32ToString(bytes32 x) private pure returns (string memory) {
	unchecked {
		bytes memory bytesString = new bytes(32);
			uint256 charCount = 0;
			for (uint256 j = 0; j < 32; j++) {
				bytes1 char = x[j];
				if (char != 0) {
					bytesString[charCount] = char;
					charCount++;
				}
			}
			bytes memory bytesStringTrimmed = new bytes(charCount);
			for (uint256 j = 0; j < charCount; j++) {
				bytesStringTrimmed[j] = bytesString[j];
			}
		return string(bytesStringTrimmed);
	}
}
```
SafeERC20Namer.sol#30-44:
```
function parseStringData(bytes memory b) private pure returns (string memory) {
	unchecked {
		uint256 charCount = 0;
		// first parse the charCount out of the data
		for (uint256 i = 32; i < 64; i++) {
			charCount <<= 8;
			charCount += uint8(b[i]);
		}

		bytes memory bytesStringTrimmed = new bytes(charCount);
		for (uint256 i = 0; i < charCount; i++) {
			bytesStringTrimmed[i] = b[i + 64];
		}
		return string(bytesStringTrimmed);
	}
}
```
