## 1. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, functions or variables by using the named imports explicitly. Plain imports will import the entire context of the imported contract which could lead into variable name conflicts etc ...

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L4-L11

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Caviar.sol#L4-L7

## 2. EVENT IS MISSING `INDEXED` FIELDS

Each event can use up to three `indexed` fields if gas is not particularly of concern. Index event fields make the fields more quickly accessible to the off-chain tools that parse events.

    event Add(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
    event Remove(uint256 baseTokenAmount, uint256 fractionalTokenAmount, uint256 lpTokenAmount);
    event Buy(uint256 inputAmount, uint256 outputAmount);
    event Sell(uint256 inputAmount, uint256 outputAmount);
    event Wrap(uint256[] tokenIds);
    event Unwrap(uint256[] tokenIds);
    event Close(uint256 closeTimestamp);
    event Withdraw(uint256 tokenId);

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L30-L37

## 3. `safeTransferFrom()` FUNCTION IS CALLED IN ERC20 TOKEN. BUT IT IS NOT A FUNCTION IN ERC20 TOKEN STANDARD. `transferFrom()` FUNCTION SHOULD BE CALLED INSTEAD.

There are two instances of this issue in Pair.sol.

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L188

            ERC20(baseToken).safeTransferFrom(msg.sender, address(this), baseTokenAmount);

            ERC20(baseToken).safeTransferFrom(msg.sender, address(this), inputAmount);

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L95

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L172

## 4. CHANGE IN COMMENT IS REQUIRED.

In the following comment the output token is referred as **fractional tokens**. But it should be corrected as **base tokens** since it is the base token which is outputted in the `sell(uint256,uint256)` function.

        // check that the outputted amount of fractional tokens is greater than the min amount

https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L188



