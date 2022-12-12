G1. https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L14
Declaring local variable ``char`` outside of the for loop can save gas.
```
bytes1 char;
for (uint256 j = 0; j < 32; j++) {
            char = x[j];
            if (char != 0) {
                bytesString[charCount] = char;
                charCount++;
            }
}
```

G2. https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L33
moving ``i++`` to unchecked block can save gas
```
for (uint256 i = 32; i < 64) {
            charCount <<= 8;
            charCount += uint8(b[i]);
            unchecked{++i};                // @audit
}
```
