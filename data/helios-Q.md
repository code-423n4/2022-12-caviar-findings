# 1st report for : Caviar.sol https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol

## 1. Incorrect Requirement in Pair Creation

Summary: 
The `create` function has a requirement that checks if the pair already exists. However, this check only compares the pair to the address 0 and not the contract's null address. As a result, the contract can never create a pair.

Impact: 
This vulnerability prevents the contract from creating any pairs.

Recommendation: 
Update the `create` function to check if the pair address is the contract's null address instead of 0. This can be done by replacing `require(pairs[nft][baseToken][merkleRoot] == address(0), "Pair already exists");` with `require(pairs[nft][baseToken][merkleRoot] == address(0x0), "Pair already exists");`.

## 2. Pair can destroy itself using unauthorized addresses

Summary: 
The `destroy` function in contract `Caviar` allows a `Pair` to destroy itself using unauthorized addresses. This can be exploited by an attacker to destroy a `Pair` contract without the correct authorization.

Impact: 
This vulnerability allows an attacker to destroy a `Pair` contract without the correct authorization. This can result in a loss of funds for users of the `Pair` contract, and can potentially disrupt the normal operation of the `Caviar` contract.

Recommendation: 
The `destroy` function should be modified to only allow the `Pair` contract that it is being called on to destroy itself. This can be accomplished by using the `selfdestruct` keyword in the `destroy` function. For example:
```
function destroy() public {
    require(msg.sender == address(this), "Only pair can destroy itself");
    selfdestruct(msg.sender);
}
```
# 2nd report for : Pair.sol https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol

## 1. Error in "add" function

Summary:

The "add" function in the contract has a bug where the transfer of base tokens is not checked for success before minting the LP tokens. This can cause the LP tokens to be minted even if the transfer of base tokens fails.

Impact:

If the transfer of base tokens fails, the LP tokens will be minted, but the user will not receive the base tokens they intended to add to the liquidity pool. This can lead to a loss of funds for the user.

Recommendation:

The "add" function should check that the transfer of base tokens is successful before minting the LP tokens. This can be done by adding a check for the return value of the "transfer" or "transferFrom" function call. If the transfer fails, the function should revert and not mint the LP tokens.

## 2. Incorrect ETH input handling in `add` function

Summary:
The `add` function in the contract incorrectly handles ETH input. If the `baseToken` is set to the address of the ETH token (`address(0)`), the function expects the caller to send ETH with the transaction. However, it does not check that the correct amount of ETH was sent, which can lead to incorrect behavior.

Impact:
If the caller does not send the correct amount of ETH with the transaction, the contract will not function correctly. This can cause incorrect token balances, lost funds, and other issues.

Recommendation:
The `add` function should check that the correct amount of ETH was sent with the transaction if the `baseToken` is set to the address of the ETH token. This can be done by adding a check that `msg.value` is equal to `baseTokenAmount` when `baseToken` is set to `address(0)`.

## 3. Incorrect token amount checks

Summary:

In the `add()` function, the code contains checks to verify that the `baseTokenAmount` and `fractionalTokenAmount` inputs are non-zero, but it fails to check that the `minLpTokenAmount` input is non-zero. This means that it is possible to call the `add()` function with a `minLpTokenAmount` of zero, which would cause the `lpTokenAmount` variable to be assigned a value of zero.

Impact:

If a user calls the `add()` function with a `minLpTokenAmount` of zero, they will be able to mint zero LP tokens, even if they provide non-zero amounts of base and fractional tokens. This can result in the LP token supply being inflated with zero-valued tokens.

Recommendation:

Add a check to verify that the `minLpTokenAmount` input is non-zero in the `add()` function. This can be done by adding the following line of code after the existing checks for `baseTokenAmount` and `fractionalTokenAmount`:
```
require(minLpTokenAmount > 0, "minLpTokenAmount is zero");
```
## 4. Using SafeTransferLib without ERC165 support

Summary: 
The contract `Pair` uses the `SafeTransferLib` library, which requires ERC165 support to function properly. However, `Pair` does not implement ERC165, and therefore may not be able to properly transfer ERC20 or ERC721 tokens.

Impact: 
If the contract is unable to transfer ERC20 or ERC721 tokens due to the lack of ERC165 support, it may not be able to fulfill its intended purpose. This could cause confusion and frustration for users of the contract.

Recommendation: 
Implement ERC165 support in the `Pair` contract to ensure that it can properly transfer ERC20 and ERC721 tokens using the `SafeTransferLib` library. This will allow the contract to function properly and avoid potential problems for users.

## 5. Reentrancy Attack

Summary: 
The `add` function of the `Pair` contract does not properly protect against reentrancy attacks.

Impact: 
An attacker could potentially call the `add` function repeatedly, causing the contract to transfer an arbitrary amount of the base or fractional tokens to the attacker. This could result in the contract being drained of funds.

Recommendation: 
To protect against reentrancy attacks, the `add` function should use the `transferFrom` function from the `SafeTransferLib` contract instead of calling `_transferFrom` directly. Additionally, the `add` function should update its internal state before calling any external functions. This would ensure that any reentrant calls to add would not be able to transfer any funds.

# 3rd report for : LpToken.sol https://github.com/code-423n4/2022-12-caviar/blob/main/src/LpToken.sol

## 1. Incorrect constructor parameters in LpToken contract

Summary: 
The LpToken contract is using the wrong parameters in the constructor of the ERC20 contract. This can cause the token to have an incorrect name, symbol, and decimals value.

Impact: 
This can lead to confusion for users of the LpToken contract, as well as potentially causing problems for any contracts that interact with it.

Recommendation: 
Update the LpToken contract to use the correct parameters in the constructor of the ERC20 contract. This will ensure that the token has the correct name, symbol, and decimals value.

## 2. Lack of input validation in LpToken contract

Summary: 
The LpToken contract is not properly validating input in the mint and burn functions. Specifically, the contract is not checking if the "to" address is non-zero, or if the "amount" is greater than zero.

Impact: 
This can allow attackers to pass in arbitrary values for the "to" address and the "amount", potentially leading to unexpected behavior or errors in the contract.

Recommendation: 
Update the mint and burn functions in the LpToken contract to properly validate input by checking that the "to" address is non-zero and that the "amount" is greater than zero. This will ensure that the contract can handle only valid input and will prevent potential errors or unexpected behavior.

# 4th report for : SafeERC20Namer.sol https://github.com/code-423n4/2022-12-caviar/blob/main/src/lib/SafeERC20Namer.sol

## 1. Invalid Data Returned from tokenSymbol() and tokenName()

Summary: 
The `tokenSymbol()` and `tokenName()` functions in the SafeERC20Namer contract may return an empty string when the ERC20 token at the specified address does not implement a `symbol()` or `name()` function. This can result in an invalid token descriptor being produced.

Impact: 
If an invalid token descriptor is produced, it could cause issues with applications that rely on the correct token symbol and name being returned. This could potentially lead to user confusion and loss of funds.

Recommendation: 
The `tokenSymbol()` and `tokenName()` functions should return a default symbol or name, rather than an empty string, when the ERC20 token does not implement a `symbol()` or `name()` function. This will ensure that a valid token descriptor is always produced.

## 2. Inefficient and Incorrect Conversion of Bytes32 to String

Summary: 
The `bytes32ToString()` function in the `SafeERC20Namer` library creates an intermediate `bytes` object to store the string representation of `bytes32` input, which is unnecessarily inefficient and can also result in incorrect string outputs.

Impact: 
This bug can cause significant inefficiency in programs that use this library and can also result in incorrect string outputs.

Recommendation: 
The `bytes32ToString()` function should be rewritten to directly convert the `bytes32` input to a `string` output without creating an intermediate `bytes` object. Additionally, the loop that converts the `bytes32` input to a `bytes` object should terminate when it reaches the end of the input string, rather than looping through all 32 bytes of the `bytes32` input. This will prevent the creation of incorrect string outputs.

## 3. Unsafe parse of symbol or name returned from external contract

Summary: 
The `callAndParseStringReturn` function in the `SafeERC20Namer` contract does not properly validate the length of the symbol or name returned from an external contract, potentially allowing an attacker to read memory beyond the bounds of the data returned.

Impact: 
If an attacker can control the values returned by a contract when `callAndParseStringReturn` is called on it, they may be able to read sensitive data from the contract calling `SafeERC20Namer`.

Recommendation: 
Properly validate the length of the data returned by an external contract before attempting to parse it. This can be done by comparing the length of the data to the expected length of the string type being returned. If the lengths do not match, return an empty string instead.

## 4. Incorrect computation of `charCount` in `parseStringData`

Summary: 
The `parseStringData` function in the `SafeERC20Namer` library computes the character count of the input string incorrectly, resulting in an incorrect output string.

Impact: 
This bug can cause the `parseStringData` function to produce an incorrect output string. Depending on how this function is used, this could lead to unpredictable behavior and potentially result in security vulnerabilities.

Recommendation: 
The `parseStringData` function should be modified to correctly compute the character count of the input string. This can be done by correctly shifting the value of `charCount` by 8 bits at each iteration of the loop, instead of by 1 bit as it is currently. For example, the loop could be rewritten as follows:
```
for (uint256 i = 32; i < 64; i++) {
    charCount <<= 8;
    charCount += uint8(b[i]);
}
```
## 5. Incorrect Fallback to Address-based Token Symbol Heuristic

Summary: 
The `tokenSymbol` function has an incorrect fallback to an address-based heuristic for determining the symbol of a token. The function returns the first 4 hex of the address string, but the comment above the function states that it should return the first 6 hex of the address string.

Impact: 
This bug can cause the `tokenSymbol` function to return incorrect token symbols. This can lead to user confusion and potentially cause problems in contracts that rely on the correct symbol being returned.

Recommendation: 
Update the `tokenSymbol` function to return the first 6 hex of the address string as stated in the comment above the function. This will ensure that the function returns the correct token symbol.

## 6. Missing input validation for bytes32ToString

Summary: 
The `bytes32ToString` function in the `SafeERC20Namer` library does not validate its input. This can lead to an out-of-bounds read when reading from `x` in the `for` loop at the beginning of the function.

Impact: 
An attacker could exploit this vulnerability by passing a carefully-crafted bytes32 value to `bytes32ToString` that would cause it to read outside the bounds of the `x` array. This could potentially result in arbitrary code execution on the Ethereum Virtual Machine (EVM), allowing the attacker to take control of the contract or steal sensitive information.

Recommendation: 
The input to `bytes32ToString` should be validated to ensure that it is a valid bytes32 value with no more than 32 elements. The for loop at the beginning of the function should be updated to ensure that it only reads up to the 32nd element of `x`, even if `charCount` is less than 32. For example, the loop could be rewritten as follows:
```
for (uint256 j = 0; j < 32 && j < charCount; j++) {
    bytes1 char = x[j];
    if (char != 0) {
        bytesString[charCount] = char;
        charCount++;
    }
}
```
Alternatively, the `charCount` variable could be initialized to 32 instead of 0 and the `if` statement at the beginning of the loop could be removed, like so:
```
function bytes32ToString(bytes32 x) private pure returns (string memory) {
    bytes memory bytesString = new bytes(32);
    uint256 charCount = 32;
    for (uint256 j = 0; j < 32; j++) {
        bytes1 char = x[j];
        if (char != 0) {
            bytesString[charCount] = char;
            charCount--;
        }
    }

    // ...
}
```
