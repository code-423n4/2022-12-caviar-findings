
### 1st Bug
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2022-12-caviar/script/CreatePair.s.sol::58 => for (uint256 i = 0; i < tokenIds.length; i++) {
```
#### Tools used
Manual code review

### 2nd Bug
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2022-12-caviar/src/Pair.sol::232 => fractionalTokenAmount = tokenIds.length * ONE;
2022-12-caviar/src/Pair.sol::252 => fractionalTokenAmount = tokenIds.length * ONE;
2022-12-caviar/src/Pair.sol::300 => remove(lpTokenAmount, minBaseTokenOutputAmount, tokenIds.length * ONE);
2022-12-caviar/src/Pair.sol::312 => inputAmount = buy(tokenIds.length * ONE, maxInputAmount);
2022-12-caviar/src/lib/SafeERC20Namer.sol::62 => if (!success || data.length == 0) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::65 => // bytes32 data always has length 32
2022-12-caviar/src/lib/SafeERC20Namer.sol::66 => if (data.length == 32) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::69 => } else if (data.length > 64) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::79 => if (bytes(symbol).length == 0) {
2022-12-caviar/src/lib/SafeERC20Namer.sol::90 => if (bytes(name).length == 0) {
```
#### Tools used
Manual code review


### 3rd Bug
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2022-12-caviar/src/lib/SafeERC20Namer.sol::77 => // 0x95d89b41 = bytes4(keccak256("symbol()"))
2022-12-caviar/src/lib/SafeERC20Namer.sol::88 => // 0x06fdde03 = bytes4(keccak256("name()"))
```
#### Tools used
Manual code review

### 4th Bug
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-12-caviar/src/Pair.sol::7 => import "solmate/utils/SafeTransferLib.sol";
2022-12-caviar/src/Pair.sol::46 => ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
```
#### Tools used
Manual code review