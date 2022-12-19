# [G-01] Remove Owned inheritance contract and replace with immutable owner

Originally:

| src/Caviar.sol:Caviar contract |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
| Deployment Cost | Deployment Size |     |     |     |     |
| 4625247 | 23078 |     |     |     |     |
| Function Name | min | avg | median | max | # calls |
| create | 913 | 3395399 | 3413705 | 3472634 | 190 |
| destroy | 2511 | 5619 | 6351 | 6351 | 10  |
| owner | 348 | 1681 | 2348 | 2348 | 12  |
| pairs | 797 | 1511 | 797 | 2797 | 14  |

| src/LpToken.sol:LpToken contract |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
| Deployment Cost | Deployment Size |     |     |     |     |
| 806769 | 5039 |     |     |     |     |
| Function Name | min | avg | median | max | # calls |
| balanceOf | 608 | 971 | 608 | 2608 | 11  |
| burn | 3047 | 11047 | 12647 | 12647 | 12  |
| mint | 24881 | 45751 | 46781 | 46781 | 89  |
| name | 1193 | 1193 | 1193 | 1193 | 1   |
| symbol | 1236 | 1236 | 1236 | 1236 | 1   |
| totalSupply | 363 | 1932 | 2363 | 2363 | 130 |

After removing Owned inheritance (and replace with immutable owner) in `Caviar.sol` and `LpToken.sol`

Example implementation for `LpToken`, (the Caviar would be similar approach, but no need the onlyOwner modifier)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

import "solmate/tokens/ERC20.sol";

contract LpToken is ERC20 {
    address immutable owner;

    modifier onlyOwner {
        require(msg.sender == owner, "Not Owner");
        _;
    }

    constructor(string memory pairSymbol)
        ERC20(string.concat(pairSymbol, " LP token"), string.concat("LP-", pairSymbol), 18)
    {
        owner = msg.sender;
    }

    function mint(address to, uint256 amount) public onlyOwner{
        _mint(to, amount);
    }

    function burn(address from, uint256 amount) public onlyOwner{
        _burn(from, amount);
    }
}
```

| src/Caviar.sol:Caviar contract |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
| Deployment Cost | Deployment Size |     |     |     |     |
| 4513477 | 22588 |     |     |     |     |
| Function Name | min | avg | median | max | # calls |
| create | 926 | 3329400 | 3347356 | 3406285 | 190 |
| destroy | 2488 | 5595 | 6328 | 6328 | 10  |
| owner | 204 | 204 | 204 | 204 | 12  |
| pairs | 819 | 1533 | 819 | 2819 | 14  |

| src/LpToken.sol:LpToken contract |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
| Deployment Cost | Deployment Size |     |     |     |     |
| 740496 | 4783 |     |     |     |     |
| Function Name | min | avg | median | max | # calls |
| balanceOf | 542 | 905 | 542 | 2542 | 11  |
| burn | 2880 | 9046 | 10480 | 10480 | 12  |
| mint | 24803 | 43808 | 44703 | 44703 | 89  |
| name | 1193 | 1193 | 1193 | 1193 | 1   |
| symbol | 1236 | 1236 | 1236 | 1236 | 1   |
| totalSupply | 363 | 1932 | 2363 | 2363 | 130 |

Gas Snapshots difference from foundry test before and after

```
testItRemovesPairFromMapping(address,address,bytes32,address) (gas: 30 (0.016%)) 
testItRemovesPairFromMapping() (gas: -7 (-0.047%)) 
testItEmitsDestroyEvent() (gas: -29 (-0.186%)) 
testItMintsLpTokensAfterInit() (gas: -1778 (-0.208%)) 
testItMintsLpTokensAfterInit(uint256,uint256) (gas: -1778 (-0.220%)) 
testItAddsBuysSellsRemovesCorrectAmount(uint256,uint256,uint256) (gas: -1800 (-0.229%)) 
testOnlyPairCanRemoveItself() (gas: -32 (-0.292%)) 
testItMintsLpTokensAfterInit() (gas: -1777 (-0.324%)) 
testItRevertsSlippageAfterInitMint() (gas: -1662 (-0.356%)) 
testItInitMintsLpTokensToSender(uint256,uint256) (gas: -1715 (-0.452%)) 
testItMintsLpTokensAfterInitWithEther() (gas: -1778 (-0.556%)) 
testItTransfersNfts() (gas: -2078 (-0.776%)) 
testItTransfersBaseTokens() (gas: -2078 (-0.788%)) 
testItInitMintsLpTokensToSender() (gas: -2144 (-0.818%)) 
testItTransfersBaseTokens() (gas: -2167 (-1.283%)) 
testItTransfersNfts() (gas: -2167 (-1.285%)) 
testItReturnsBaseTokenAmountAndFractionalTokenAmount() (gas: -2167 (-1.332%)) 
testItBurnsLpTokens() (gas: -2299 (-1.365%)) 
testItSellsWithMerkleProof() (gas: -66427 (-1.452%)) 
testItAddsWithMerkleProof() (gas: -66427 (-1.521%)) 
testItRevertsSlippageAfterInitMint() (gas: -2078 (-1.552%)) 
testItAddsWithMerkleProof() (gas: -66349 (-1.558%)) 
testItTransfersBaseTokens() (gas: -2078 (-1.646%)) 
testItEmitsAddEvent() (gas: -2078 (-1.658%)) 
testItTransfersFractionalTokens() (gas: -2078 (-1.671%)) 
testItInitMintsLpTokensToSender() (gas: -2144 (-1.716%)) 
testItSetsSymbolsAndNames() (gas: -66349 (-1.901%)) 
testItSavesPair(address,address,bytes32) (gas: -66327 (-1.906%)) 
testItRevertsIfDeployingSamePairTwice() (gas: -66335 (-1.935%)) 
testItEmitsCreateEvent() (gas: -66348 (-1.935%)) 
testItSavesPair() (gas: -66326 (-1.937%)) 
testItReturnsPair() (gas: -66348 (-1.938%)) 
testItTransfersEther() (gas: -2078 (-2.118%)) 
testItTransfersBaseTokens() (gas: -2167 (-2.124%)) 
testItTransfersFractionalTokens() (gas: -2167 (-2.164%)) 
testItEmitsRemoveEvent() (gas: -2167 (-2.169%)) 
testItBurnsLpTokens() (gas: -2299 (-2.261%)) 
testItReturnsBaseTokenAmountAndFractionalTokenAmount() (gas: -2167 (-2.265%)) 
testItEmitsWithdrawEvent() (gas: -1853 (-2.497%)) 
testItTransfersNftsAfterWithdraw() (gas: -1854 (-2.506%)) 
testItTransfersEther() (gas: -2167 (-3.099%)) 
testItReturnsBaseTokenAmountAndFractionalTokenAmount(uint256) (gas: -2167 (-4.247%)) 
testItEmitsCloseEvent() (gas: -2173 (-5.218%)) 
testCannotWithdrawIfNotAdmin() (gas: -2317 (-5.445%)) 
testCannotWithdrawIfNotEnoughTimeElapsed() (gas: -2317 (-5.457%)) 
testExitSetsCloseTimestamp() (gas: -2173 (-5.610%)) 
testCannotExitIfNotAdmin() (gas: -2144 (-12.581%)) 
testCannotWithdrawIfNotClosed() (gas: -2144 (-13.471%)) 
Overall gas change: -669472 (-108.059%)
```

In short gas for deployment diff:

- `Caviar` diff 23078 - 22588 = 490
  
- `LpToken` diff 5039 - 4783 = 256