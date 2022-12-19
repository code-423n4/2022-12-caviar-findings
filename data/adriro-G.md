## LpToken owner can be an immutable variable

The `LpToken` contract uses the `Owned` contract from solmate to handle access protection to the `mint` and `burn` functions. There's no benefit from using a storage variable to represent the owner of this contract, since the intended owner of this token is always the `Pair` contract itself, which doesn't transfer the ownership.

Consider using an immutable variable that is initialized at construction time. This change could save one storage read in every call `mint` and `burn`.
