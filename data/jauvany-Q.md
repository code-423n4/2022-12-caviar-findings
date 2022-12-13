USE .SELECTOR INSTEAD OF HEX NUMBER

Impact

In the contract SafeERC20Namer.sol a 2 function selects are encoded as hexadecimal numbers.
Solidity also has the keyword ".selector" which makes the code easier to read and less error prone.


Proof of Concept

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L76

// 0x95d89b41 = bytes4(keccak256("symbol()"))
string memory symbol = callAndParseStringReturn(token, 0x95d89b41);

// 0x06fdde03 = bytes4(keccak256("name()"))
string memory name = callAndParseStringReturn(token, 0x06fdde03);



Tools Used
Editor

Recommended Mitigation Steps

Alternative implementations:
IERC20Metadata.symbol.selector // 0x95d89b41 = bytes4(keccak256("symbol()"))
IERC20Metadata.name.selector // 0x06fdde03 = bytes4(keccak256("name()"))
