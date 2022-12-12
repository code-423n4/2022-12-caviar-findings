Trying to withdraw liquidity from the [app/liquidity/withdraw page](https://goerli.caviar.sh/app/liquidity/withdraw) without selecting an NFT charges gas for a transaction without actually transferring anything at all.

Here is an example transaction where no NFTs were selected:
[https://goerli.etherscan.io/tx/0x9079fa686b97403d33f5e146b4fa860ea7857a5aa105c87014601c3df961ffc5](https://goerli.etherscan.io/tx/0x9079fa686b97403d33f5e146b4fa860ea7857a5aa105c87014601c3df961ffc5)


Here is an example of a valid transaction to withdraw 1 NFT of liquidity:
[https://goerli.etherscan.io/tx/0x77cda9ec669bccf49272d627a62ab3a0c304d090143533c8dbe086ba5a5aa468](https://goerli.etherscan.io/tx/0x77cda9ec669bccf49272d627a62ab3a0c304d090143533c8dbe086ba5a5aa468)