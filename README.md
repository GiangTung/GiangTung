# Spacetoken
NFT Marketplace on the Cardano Blockchain. Powered by Plutus Smart Contracts
A simple contract that allows sales of NFTs. The seller (owner of an NFT) constructs the contract with parameters unique to the sale and locks the NFT there.
A buyer can then unlock the NFT by submitting a transaction verifying the several requirements defined in the validator.
***
### Test in emulator
* Clone the repository
* Run `cabal repl` in the repo
* Run `:l src/Market/Trace.hs` in the repl
* Run `test`
* To modify the testing scenario, open and modify the `src/Market/Trace.hs` file
***
### To compile to .plutus code
* Run cabal run market-plutus
***
### To test against a validator
#### Do the following only the first time
* Start a testnet node a note where is the node.socket file
* CARDANO_NODE_SOCKET_PATH=/absolute/path/to/node.socket
* testnet="testnet-magic 1097911063"
* Create a folder for wallet 1 w1 the seller and for wallet 2 w2 the buyer
* Generate payment files in both folders :
* cardano-cli address key-gen --verification-key-file payment.vkey --signing-key-file payment.skey
* cardano-cli address build --payment-verification-key-file payment.vkey --out-file payment.addr --$testnet
* Export protocol params in both folders cardano-cli query protocol-parameters --$testnet --out-file protocol.json
* I suggest downloading Daedalus testnet Daedalus-testnet
* Fund the new Daedalus wallet using the testnet faucet at faucet
* From daedalus, fund the two cli addresses (w1 and w2) (with the faucet you should get 1000ADA so fund w1 with 500 and w2 with 500)


## Contract usage
The Martify contract for marketplaces is being used by several marketplaces currently live on Cardano.
The list of marketplaces based on our contracts is :
* https://jpg.store
* https://martify.io
* https://filthyrichhorses.com/marketplace
