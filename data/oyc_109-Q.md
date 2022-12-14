

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-12-caviar/src/Caviar.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/LpToken.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/Pair.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/lib/SafeERC20Namer.sol::2 => pragma solidity ^0.8.17;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-12-caviar/src/Pair.sol::346 => closeTimestamp = block.timestamp + CLOSE_GRACE_PERIOD;
2022-12-caviar/src/Pair.sol::367 => require(block.timestamp >= closeTimestamp, "Not withdrawable yet");
```

## [L-03] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2022-12-caviar/src/Pair.sol::469 => bool isValid = MerkleProofLib.verify(proofs[i], merkleRoot, keccak256(abi.encodePacked(tokenIds[i])));
```

## [L-04] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-12-caviar/src/LpToken.sol::20 => _mint(to, amount);
2022-12-caviar/src/Pair.sol::233 => _mint(msg.sender, fractionalTokenAmount);
```

## [N-01] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-12-caviar/src/Pair.sol::30 => event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
2022-12-caviar/src/Pair.sol::31 => event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
2022-12-caviar/src/Pair.sol::32 => event Buy(uint256 inputAmount, uint256 outputAmount);
2022-12-caviar/src/Pair.sol::33 => event Sell(uint256 inputAmount, uint256 outputAmount);
2022-12-caviar/src/Pair.sol::34 => event Wrap(uint256[] tokenIds);
2022-12-caviar/src/Pair.sol::35 => event Unwrap(uint256[] tokenIds);
2022-12-caviar/src/Pair.sol::36 => event Close(uint256 closeTimestamp);
2022-12-caviar/src/Pair.sol::37 => event Withdraw(uint256 tokenId);
```

## [N-02] Missing NatSpec

Code should include NatSpec

```
2022-12-caviar/src/lib/SafeERC20Namer.sol::1 => // SPDX-License-Identifier: MIT
```

