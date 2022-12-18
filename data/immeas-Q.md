# low

## L-1 wrong price for low decimal tokens with a lot of low value NFTs

If there's a large enough discrepancy in price and decimals the price and quote calulactions don't work anymore.

It is unlikely but possible with a combination of "worthless" NFTs and tokens like `USDC`

### Proof of Concept

```javascript
    function testLowDecimalTokensCalculatesWrongPrice() public {
        Token6D t6 = new Token6D();
        Pair _pair = c.create(address(bayc), address(t6), bytes32(0));

        deal(address(_pair),address(this),10_000_000e18,true);
        deal(address(t6),address(this),100e6,true);

        t6.approve(address(_pair),type(uint256).max);

        // a bunch of really cheap domains as an example
        _pair.add(1e6, 1_000_000e18 + 1, 1);

        // 0
        console.log(_pair.price());
        
        // 0 a user could buy all for nothing
        console.log(_pair.buyQuote(1e18));
    }
```
### mitigation

uniswap gets around this by never doing any price calculation and only doing multiplication when swapping.

Either do this, or normalize low decimal tokens