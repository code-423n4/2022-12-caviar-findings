## [Info] Avoid using `address(this).balance`

[`Pair.sol#L479`](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L479)

A contract should avoid to have dependencies on the exact balance amount because it can be artificially manipulated. For this case, a variable should be used to increment value in payable functions, thus, safely tracking the deposited ether. 

