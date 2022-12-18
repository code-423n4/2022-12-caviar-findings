## [QA-01] Use stable pragma statement

Using a floating pragma `^0.8.17` statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.

## [QA-02] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

[Caviar.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L4-L7),
[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L4-L11),
[LpToken.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L4-L5),
[SafeERC20Namer.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L4)

## [QA-03] USE \_SAFEMINT INSTEAD OF \_MINT

[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L233)
[LpToken.sol](https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L20)

```
 _mint(msg.sender, fractionalTokenAmount);
 _mint(to, amount);
```

According to openzepplin’s [ERC721](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-)

#### Recommended Mitigation Steps

Use \_safeMint whenever possible instead of \_mint

## [QA-04] Typos in code

[Pair.sol](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465)

```
if (merkleRoot == bytes23(0)) return;
```

Change bytes23 to bytes32.
