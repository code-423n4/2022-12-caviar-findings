QA:
ONE is a poorly named constant – perhaps use TO_18_DECIMALS or write a helper fn
Without context, I would expect ONE = 1
Caviar owner contract should support a safe transfer to prevent invalid addresses from receiving ownerships, locking the contract


Claims (Low):
A user could flashloan the token / ETH, withdraw all staked NFTs, claim any mints that that the NFT has. 
Potential ways to fix this MEV issue would be if (`tx.origin != msg.sender`) {} require that the user commits to a withdraw in one transaction and finishes the withdraw in another transaction. This prevents both sandwich attacks and also loaning out all NFTs to claim some value (such as with apecoin) and allows users that are the ones executing the transaction to be unaffected (or just add this restriction for all withdraws). It would also alleviate any potential oracle and sandwich attacks common to Pair based trading contracts.
