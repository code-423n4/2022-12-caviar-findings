
# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1     | Enable caviar owner to withdraw multiple NFTs in a single transaction | NC | 1 |
| 2     | Non-library/interface files should use fixed compiler versions, not floating ones | NC| 3  |
| 3     | Duplicated `require()` checks should be refactored to a modifier  | NC | 2 |


## Findings

### 1- Enable caviar owner to withdraw multiple NFTs in a single transaction :

#### Risk : NON CRITICAL

In the current implementation of the `Pair` contract the owner can use the `withdraw` function to pull one NFT at the time which can become very hard when the contract holds many of them, it could be better if the owner could pass an array of token ids that he wants to withdraw and get them all in a single call.

The `withdraw` function can be modified as follow :

```
function withdraw(uint256[] calldata tokenIds) public {
    // check that the sender is the caviar owner
    require(caviar.owner() == msg.sender, "Withdraw: not owner");

    // check that the close period has been set
    require(closeTimestamp != 0, "Withdraw not initiated");

    // check that the close grace period has passed
    require(block.timestamp >= closeTimestamp, "Not withdrawable yet");

    // transfer the nfts to the caviar owner
    for (uint256 i = 0; i < tokenIds.length; i++) {
        ERC721(nft).safeTransferFrom(
            address(this),
            msg.sender,
            tokenIds[i]
        );
    }

    emit Withdraw(tokenIds);
}
```


### 2- Non-library/interface files should use fixed compiler versions, not floating ones :

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

check this source : https://swcregistry.io/docs/SWC-103

#### Risk : NON CRITICAL

#### Proof of Concept

There are 3 instances of this issue :

```
File: src/Caviar.sol

pragma solidity ^0.8.17;

File: src/LpToken.sol

pragma solidity ^0.8.17;

File: src/Pair.sol

pragma solidity ^0.8.17;
```

#### Mitigation
Lock the pragma version to the same version as used in the other contracts and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.


### 3- Duplicated `require()` checks should be refactored to a modifier :

`require()` checks statement used multiple times inside a contract should be refactored to a modifier for better readability.

#### Risk : NON CRITICAL

#### Proof of Concept

There are 2 instances of this issue :

```
File: src/Pair.sol

437     require(caviar.owner() == msg.sender, "Close: not owner");
455     require(caviar.owner() == msg.sender, "Withdraw: not owner");
```

Those checks should be replaced by a `onlyCaviarOwner()` modifier as follow :

```
modifier onlyCaviarOwner(){
    require(caviar.owner() == msg.sender, "not caviar owner");
    _;
}
```