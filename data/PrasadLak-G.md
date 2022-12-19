## PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

There are 9 instances of this issue:

	File: src/Caviar.sol   

	28:	function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {

	49:	function destroy(address nft, address baseToken, bytes32 merkleRoot) public {

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L28

	File: src/Pair.sol

	275:	function nftAdd(uint256 baseTokenAmount,uint256[] calldata tokenIds,uint256 minLpTokenAmount,bytes32[][] calldata proofs) 
		public payable returns (uint256 lpTokenAmount) {

	294:	function nftRemove(uint256 lpTokenAmount, uint256 minBaseTokenOutputAmount, uint256[] calldata tokenIds) public
        	returns (uint256 baseTokenOutputAmount, uint256 fractionalTokenOutputAmount)

	310	function nftBuy(uint256[] calldata tokenIds, uint256 maxInputAmount) public payable returns (uint256 inputAmount) {

	323	function nftSell(uint256[] calldata tokenIds, uint256 minOutputAmount, bytes32[][] calldata proofs) public
        	returns (uint256 outputAmount)

	341	function close() public {

	359	function withdraw(uint256 tokenId) public {

	390	function price() public view returns (uint256) {

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L275


## ADD UNCHECKED {} FOR ITERATOR WHERE THE OPERANDS CANNOT OVERFLOW BECAUSE ITS ALLWAYS BELOW THE GIVEN NUMBER.
	
for loops j++ and i++ can be set to UNCHECKED{++j} and UNCHECKED{++i}


There are 7 instances of this issue:

	File: src/lib/SafeERC20Namer.sol

	13	for (uint256 j = 0; j < 32; j++) {

	17	charCount++;

	22	for (uint256 j = 0; j < charCount; j++) {

	33	for (uint256 i = 32; i < 64; i++) {

	39	for (uint256 i = 0; i < charCount; i++) {

https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol#L13

	File: src/Pair.sol

	238	for (uint256 i = 0; i < tokenIds.length; i++) {

	258	for (uint256 i = 0; i < tokenIds.length; i++) {

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L238


## STATE VARIABLES SHOULD BE CACHED IN STACK VARIABLES RATHER THAN RE-READING THEM FROM STORAGE

Here read same state variable multiple times in same functions. 

There are 4 instances of this issue:

	File: src/Pair.sol

	74	require(baseToken == address(0) ? msg.value == baseTokenAmount : msg.value == 0, "Invalid ether input");
	93	if (baseToken != address(0)) { 
	95.	ERC20(baseToken).safeTransferFrom(msg.sender, address(this), baseTokenAmount);


	132	if (baseToken == address(0)) {
	137	ERC20(baseToken).safeTransfer(msg.sender, baseTokenOutputAmount);

	151	require(baseToken == address(0) ? msg.value == maxInputAmount : msg.value == 0, "Invalid ether input");
	166	if (baseToken == address(0)) {
	172	ERC20(baseToken).safeTransferFrom(msg.sender, address(this), inputAmount);

	198	if (baseToken == address(0)) {
	203	ERC20(baseToken).safeTransfer(msg.sender, outputAmount);

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L74


## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

There are 1 instances of this issue:

	File: src/Pair.sol

	448	balanceOf[from] -= amount;

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L448



## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

if a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as
payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

There are 4 instances of this issue:      

	File: src/Pair.sol

	341	function close() public {

	359	function withdraw(uint256 tokenId) public {

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L341

	File: src/LpToken.sol

	19	function mint(address to, uint256 amount) public onlyOwner {
	26	function burn(address from, uint256 amount) public onlyOwner {

https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol#L19


## SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

There are 1 instances of this issue:  

	File: src/Pair.sol

	71	require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71