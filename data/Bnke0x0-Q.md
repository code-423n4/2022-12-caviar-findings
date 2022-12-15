

### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-12-caviar/src/Pair.sol::47 => nft = _nft;
2022-12-caviar/src/Pair.sol::48 => baseToken = _baseToken; // use address(0) for native ETH
2022-12-caviar/src/Pair.sol::49 => merkleRoot = _merkleRoot;
```




### [L02] `_safeMint()` should be used rather than `_mint()` wherever possible


#### Findings:
```
2022-12-caviar/src/LpToken.sol::20 => _mint(to, amount);
2022-12-caviar/src/Pair.sol::233 => _mint(msg.sender, fractionalTokenAmount);
```



### [L03] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.
#### Findings:
```
2022-12-caviar/src/Caviar.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/LpToken.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/Pair.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/lib/SafeERC20Namer.sol::2 => pragma solidity ^0.8.17;
```



### `Non-Critical Issues`






### [N01] constants should be defined rather than using magic numbers


#### Findings:
```
2022-12-caviar/src/LpToken.sol::13 => ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
2022-12-caviar/src/Pair.sol::20 => uint256 public constant ONE = 1e18;
2022-12-caviar/src/Pair.sol::399 => return (outputAmount * 1000 * baseTokenReserves()) / ((fractionalTokenReserves() - outputAmount) * 997);
2022-12-caviar/src/Pair.sol::407 => uint256 inputAmountWithFee = inputAmount * 997;
2022-12-caviar/src/Pair.sol::408 => return (inputAmountWithFee * baseTokenReserves()) / ((fractionalTokenReserves() * 1000) + inputAmountWithFee);
2022-12-caviar/src/lib/SafeERC20Namer.sol::55 => return Strings.toHexString(uint160(token) >> (160 - 4 * 4));
```




### [N02] Event is missing `indexed` fields


#### Findings:
```
2022-12-caviar/src/Pair.sol::30 => event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
2022-12-caviar/src/Pair.sol::31 => event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
2022-12-caviar/src/Pair.sol::32 => event Buy(uint256 inputAmount, uint256 outputAmount);
2022-12-caviar/src/Pair.sol::33 => event Sell(uint256 inputAmount, uint256 outputAmount);
```




### [N03] Unused file


#### Findings:
```
2022-12-caviar/src/Caviar.sol::1 => // SPDX-License-Identifier: MIT
2022-12-caviar/src/LpToken.sol::1 => // SPDX-License-Identifier: MIT
2022-12-caviar/src/Pair.sol::1 => // SPDX-License-Identifier: MIT
2022-12-caviar/src/lib/SafeERC20Namer.sol::1 => // SPDX-License-Identifier: MIT
```




### [N04] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .#### Findings:
```
2022-12-caviar/src/Caviar.sol::28 => function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {
2022-12-caviar/src/Caviar.sol::49 => function destroy(address nft, address baseToken, bytes32 merkleRoot) public {
2022-12-caviar/src/LpToken.sol::19 => function mint(address to, uint256 amount) public onlyOwner {
2022-12-caviar/src/LpToken.sol::26 => function burn(address from, uint256 amount) public onlyOwner {
2022-12-caviar/src/Pair.sol::147 => function buy(uint256 outputAmount, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {
2022-12-caviar/src/Pair.sol::182 => function sell(uint256 inputAmount, uint256 minOutputAmount) public returns (uint256 outputAmount) {
2022-12-caviar/src/Pair.sol::248 => function unwrap(uint256[] calldata tokenIds) public returns (uint256 fractionalTokenAmount) {
2022-12-caviar/src/Pair.sol::280 => ) public payable returns (uint256 lpTokenAmount) {
2022-12-caviar/src/Pair.sol::310 => function nftBuy(uint256[] calldata tokenIds, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {
2022-12-caviar/src/Pair.sol::341 => function close() public {
2022-12-caviar/src/Pair.sol::359 => function withdraw(uint256 tokenId) public {
2022-12-caviar/src/Pair.sol::379 => function baseTokenReserves() public view returns (uint256) {
2022-12-caviar/src/Pair.sol::383 => function fractionalTokenReserves() public view returns (uint256) {
2022-12-caviar/src/Pair.sol::390 => function price() public view returns (uint256) {
2022-12-caviar/src/Pair.sol::398 => function buyQuote(uint256 outputAmount) public view returns (uint256) {
2022-12-caviar/src/Pair.sol::406 => function sellQuote(uint256 inputAmount) public view returns (uint256) {
2022-12-caviar/src/Pair.sol::417 => function addQuote(uint256 baseTokenAmount, uint256 fractionalTokenAmount) public view returns (uint256) {
2022-12-caviar/src/Pair.sol::435 => function removeQuote(uint256 lpTokenAmount) public view returns (uint256, uint256) {
```



### [N05] Numeric values having to do with time should use time units for readability

#### Impact
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-12-caviar/src/Pair.sol::21 => uint256 public constant CLOSE_GRACE_PERIOD = 7 days;
```



### [N06] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2022-12-caviar/src/Caviar.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/LpToken.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/Pair.sol::2 => pragma solidity ^0.8.17;
2022-12-caviar/src/lib/SafeERC20Namer.sol::2 => pragma solidity ^0.8.17;
```







