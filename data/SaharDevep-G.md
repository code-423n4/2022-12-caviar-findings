# c4udit Report

## Files analyzed
- examples\Caviar.sol
- examples\LpToken.sol
- examples\Pair.sol

## Issues found


### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
examples\Pair.sol::7 => import "solmate/utils/SafeTransferLib.sol";
examples\Pair.sol::46 => ) ERC20(string.concat(nftName, " fractional token"), string.concat("f", nftSymbol), 18) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)