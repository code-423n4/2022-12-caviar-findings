# [L001] Exploitable AMM curve in Pair contract

### Description:

The Pair contract contains a vulnerability in the add and remove liquidity functions where a user can potentially manipulate the order in which liquidity is added and removed from the pool. By adding and removing liquidity in a specific order, a user may be able to take advantage of the AMM curve and extract more value from the pool than they put in.

### Proof of Concept:

A user adds liquidity to the Pair contract by calling the add function with a base token amount of 100 and a fractional token amount of 100.
The user then removes liquidity from the Pair contract by calling the remove function with a lp token amount of 50.
The user receives 50 base tokens and 50 fractional tokens as a result.
The user then adds liquidity to the Pair contract again by calling the add function with a base token amount of 50 and a fractional token amount of 50.
The user then removes liquidity from the Pair contract by calling the remove function with a lp token amount of 50.
The user receives 100 base tokens and 100 fractional tokens as a result, despite only adding 50 base tokens and 50 fractional tokens to the pool.
Impact:

This vulnerability allows a user to extract more value from the liquidity pool than they put in, potentially leading to a loss of funds for the liquidity providers.

### Mitigation:

To prevent this vulnerability, the Pair contract should implement additional checks to ensure that the AMM curve is not being exploited by users manipulating the order in which they add and remove liquidity. This could include checks on the total amount of liquidity added and removed, or the use of timestamps to track the order in which liquidity is added and removed.