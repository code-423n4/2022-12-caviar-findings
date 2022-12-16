## Low 1: Destroyed pair can be re-deployed

Caviar allows owner to destroy an existing pair:

```solidity
    function destroy(address nft, address baseToken, bytes32 merkleRoot) public {
        // check that a pair can only destroy itself
        require(msg.sender == pairs[nft][baseToken][merkleRoot], "Only pair can destroy itself");

        // delete the pair
        delete pairs[nft][baseToken][merkleRoot];
    }
```

However, once a pair is destroyed, **anyone** can redeploy it right after:

```solidity
    function create(address nft, address baseToken, bytes32 merkleRoot) public returns (Pair pair) {
        // check that the pair doesn't already exist
        require(pairs[nft][baseToken][merkleRoot] == address(0), "Pair already exists");
```

Given Caviar uses CREATE, and not CREATE2, there will be two deployments of the same pair. Caviar will point to the latest deployment. This might cause confusion in the user interface, and can even lead to the deployer of the latest deployment to trick other users for profit.

Consider only allowing the owner to re-deploy a destroyed pair.

## Low 2: Merkleproof is not verified during unwrapping NFTs

Only NFT IDs in the MerkleTree can be wrapped, however any NFT ID can be unwrapped, as there is no proof check during unwrapping:

```solidity
    function unwrap(uint256[] calldata tokenIds) public returns (uint256 fractionalTokenAmount) {
        // *** Effects *** //

        // burn fractional tokens from sender
        fractionalTokenAmount = tokenIds.length * ONE;
        _burn(msg.sender, fractionalTokenAmount);

        // *** Interactions *** //

        // transfer nfts to sender
        for (uint256 i = 0; i < tokenIds.length; i++) {
            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
        }

        emit Unwrap(tokenIds);
    }
```

This asymmetry will in effect work as burning: The more invalid IDs unwrapped, the same amount of valid IDs will be stuck in the contract. This can lead to user errors where a less valuable NFT ID is unwrapped.

## Low 3: Possible re-entrancy during unwrap

Unwrap has a loop of safeTransferFrom, which will allow msg.sender to re-enter to the contract in any iteration of the for loop.


```solidity
        // transfer nfts to sender
        for (uint256 i = 0; i < tokenIds.length; i++) {
            ERC721(nft).safeTransferFrom(address(this), msg.sender, tokenIds[i]);
        }
```

Reentering during the for loop will have the state imbalance where the totalSupply of fractional tokens can be less than the NFT balance of the contract. Although there seems to be no exploitable way currently, Caviar team should be mindful in case a function allowing surplus NFTs to be claimed by anyone. You can refer to this example: https://twitter.com/shunduquar/status/1602927716822646784

## Low 4: Incorrect type is used

In `_validateTokenIds`, `bytes23` instead of `bytes32` is used.

```solidity
        if (merkleRoot == bytes23(0)) return;
```

Use the correct type.

## Low 5: Loss of merkle tree could make the pair unusable

To wrap an NFT, users must provide the merkle proof for the token ID belonging to the merkle root. If the merkle tree is lost (e.g. Caviar server gets wiped), then people will not be able to wrap tokens anymore, making the pair unusable. Consider having an immutable state variable, that links to an IPFS where the whole merkle tree is stored. The users should then be encouraged to also download and pin the merkle tree.

## Low 6: Initial 1000 liquidity is not burned

In UniswapV2, the initial 1000 liquidity tokens are permanent burned to prevent the issue described below:

```
                                                       [I]t        is
possible for the  value of a liquidity pool share  to grow over time,
either by accumulating trading fees or through “donations” to the
liquidity pool. In theory, this could result in a situation where the
value of  the minimum quantity  of liquidity pool shares  (1e-18 pool
shares)  is  worth so  much  that  it  becomes infeasible  for  small
liquidity providers to provide any liquidity
```

Consider following the same pattern (burning 1000 liquidity tokens during initial mint) to reduce the risk of this issue.

## Info 1: A comment on the architecture

Caviar allows users to wrap and unwrap a subset of IDs from an NFT to ERC20 tokens, then use those ERC20s in a custom AMM. The AMM is identical in mechanism to UniswapV2. Given that, there might be some advantages to instead letting Caviar only handle wrapping and unwrapping NFTs, and delegating AMM to the actual UniswapV2 deployment, or a clone/fork of UniswapV2. In this model, the atomic transactions (e.g. wrap->[sell|add], [buy|remove]->unwrap) can still be achieved through a custom router. The advantages of this model would be improved security guarantees due to battle-testedness of UniswapV2, improved compatibility/composability with the rest of the DeFi ecosystem, and the ability to use the variety of already-existing tooling for UniswapV2.

## Info 2: No fees paid to the protocol

There is a 0.3% fee on each swap on the AMM. The protocol receives no fees. Ensure this is intentional.

## Info 3: Redundant function

There is a `baseTokenReserves` function that returns `_baseTokenReserves` function, without any change.

```solidity
    function baseTokenReserves() public view returns (uint256) {
        return _baseTokenReserves();
    }
```

In the code, only `price()` function uses the internal `_baseTokenReserves`, while everywhere else the public `baseTokenReserves` is used. Consider removing `_baseTokenReserves` function and only use the public one.

## Info 4: `remove` function does not check zero amount

During `add` it is checked that the input amounts are non-zero:

```solidity
        // check the token amount inputs are not zero
        require(baseTokenAmount > 0 && fractionalTokenAmount > 0, "Input token amount is zero");
```

However, in `remove` there is no check that the input `lpTokenAmount` is non-zero. Consider adding the appropriate check to revert early in the function.
