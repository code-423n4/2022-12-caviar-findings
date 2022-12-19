1. **USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT FILE.SOL`**
    
    Location:
    
    - [Caviar#L4-L7](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Caviar.sol#L4-L7)
    - [LpToken#L4-L5](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/LpToken.sol#L4-L5)
    - [Pair#L4-L11](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L4-L11)
    
    Example, you can replace [Pair#L10](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L10) with `import {LpToken} from "./LpToken.sol";`
    
2. Fail in getting `lpToken`
    
    Location: [Pair#L275-L286](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L275-L286)
    
    When performing `nftAdd`, it is possible that the user send their nfts under the `wrap` function but however does not getting `lpToken` because failure in `add` function.
    Case 1: `Base Token = 0` under [Pair#71](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L71)
    
    Case 2: `LpTokenAmount <= minLpTokenAmount` under [Pair#80](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L80)
    
3. bytes32 (Typo)
    
    Location: [Pair#L465](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/Pair.sol#L465)