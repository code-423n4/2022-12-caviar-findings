# Report

---

## Caviar Contest Gas Optimizations 

---

|       | Issue                        | Instances |
| ----- | ---------------------------- | --------- |
| GAS-1 | USE UNCHECK IN THE INCREMENT | 1         |

---

Total: 1 instances over 1 issue with estimation that 1809 of gas is saved on deployment.

#### [GAS-1] USE UNCHECK IN THE INCREMENT

We should be using Uncheck in this one, using it we will be able to shape 1809 of gas in the deployment.

```
File: /code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol
10:   function bytes32ToString(bytes32 x) private pure returns (string memory) {
11:       bytes memory bytesString = new bytes(32);
12:       uint256 charCount = 0;
13:        for (uint256 j = 0; j < 32; j++) {
14:            bytes1 char = x[j];
15:            if (char != 0) {
16:                bytesString[charCount] = char;
17:                charCount++;
            }
        }

```

- https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L17

#### Correction

USE UNCHECK

```
File: /code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol
10:   function bytes32ToString(bytes32 x) private pure returns (string memory) {
11:       bytes memory bytesString = new bytes(32);
12:       uint256 charCount = 0;
13:        for (uint256 j = 0; j < 32; j++) {
14:            bytes1 char = x[j];
15:            if (char != 0) {
16:                bytesString[charCount] = char;
17:                unchecked{charCount++;}
            }
        }

```
