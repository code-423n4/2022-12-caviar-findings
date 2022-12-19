# QA Report

## Use fixed compiler version
A floating pragma risks a different compiler version being used in production vs testing, which poses security risks.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L2
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L2
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L2

<br>

## Use two-step owner transfers
The functionality to transfer ownership in `Caviar` and `LpToken` is implemented by the `transferOwnership` function in the solmate `Owned` contract. If an erroneous address is passed as a parameter by mistake, then ownership of the contract may be irreversibly lost. A more safe approach would be to use a two-step setup, where the owner would call one function to set a new pending owner, who would then be able to call another function to accept ownership. This can be implemented simply by inheriting from OpenZeppelin's `Ownable2Step` contract instead of solmate's `Owned`.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L12
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L10

<br>

## Events are missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields).

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L30-L37

<br>

## Missing checks for zero address
`_transferFrom` ought to check whether `to` is the zero address.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L447

`Caviar:create` ought to check whether `nft` is the zero address.
- https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28