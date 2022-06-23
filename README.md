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
### To test against a validator
#### Do the following only the first time
* Start a testnet node a note where is the node.socket file
* `CARDANO_NODE_SOCKET_PATH=/absolute/path/to/node.socket`
* `testnet="testnet-magic 1097911063"`
* Create a folder for wallet 1 `w1` the seller and for wallet 2 `w2` the buyer
* Generate payment files in both folders :
*  * `cardano-cli address key-gen --verification-key-file payment.vkey --signing-key-file payment.skey`
*  * `cardano-cli address build --payment-verification-key-file payment.vkey --out-file payment.addr --$testnet`
* Export protocol params in both folders `cardano-cli query protocol-parameters --$testnet --out-file protocol.json`
* I suggest downloading <a href="https://testnets.cardano.org/en/testnets/cardano/get-started/wallet/">Daedalus-testnet</a>
* Fund the new Daedalus wallet using the testnet faucet at <a href="https://developers.cardano.org/docs/integrate-cardano/testnet-faucet">faucet</a>
* From daedalus, fund the two cli addresses (`w1` and `w2`) (with the faucet you should get 1000ADA so fund `w1` with 500 and `w2` with 500)
##### Now we have to mint some tokens (only in w1) that will be the NFTs we sell to test the validator
* Go in `w1` folder and execute `address=$(cat payment.addr)`
* Run `cardano-cli query utxo --address $address --$testnet`
* `txhashAda=< here put the txhash#txid that contains a lot of ada >` example : `txhashAda=8d866294eb8ff438858d4e825ee266a6c187f62d35c10ffc3ee1aa0f661e1397#1`
* `funds=< here put the funds sitting at the utxo hash you just used above (exact number) >`
* `fee=3000000`
* `output=0`
* `mkdir policy`
* `cardano-cli address key-gen --verification-key-file policy/policy.vkey --signing-key-file policy/policy.skey`
* `touch policy/policy.script && echo "" > policy/policy.script`
* `echo "{" >> policy/policy.script`
* `echo " \"keyHash\": \"$(cardano-cli address key-hash --payment-verification-key-file policy/policy.vkey)\"," >> policy/policy.script`
* `echo " \"type\": \"sig\"" >> policy/policy.script`
* `echo "}" >> policy/policy.script`
* `cardano-cli transaction policyid --script-file ./policy/policy.script >> policy/policyID`
* `policyid=$(cat policy/policyID)`
* `cardano-cli transaction build-raw --fee $fee --tx-in $txhashAda --tx-out $address+$output+"100 $policyid.Vendere" --mint="100 $policyid.Vendere" --minting-script-file policy/policy.script --out-file matx.raw` this will mint 100 Vendere tokens
* `fee=$(cardano-cli transaction calculate-min-fee --tx-body-file matx.raw --tx-in-count 1 --tx-out-count 1 --witness-count 1 --$testnet --protocol-params-file protocol.json | cut -d " " -f1)`
* `output=$(expr $funds - $fee)`
* `cardano-cli transaction build-raw --fee $fee --tx-in $txhashAda --tx-out $address+$output+"100 $policyid.Vendere" --mint="100 $policyid.Vendere" --minting-script-file policy/policy.script --out-file matx.raw`
* `cardano-cli transaction sign --signing-key-file payment.skey --signing-key-file policy/policy.skey --$testnet --tx-body-file matx.raw --out-file matx.signed`
* `cardano-cli transaction submit --tx-file matx.signed --$testnet`
* `cardano-cli query utxo --address $address --$testnet`
* Now you should have 100 Vendere tokens at your `w1` wallet (it can take some time to appear). We'll be using those to test our validator

## Contract usage
The Martify contract for marketplaces is being used by several marketplaces currently live on Cardano.
The list of marketplaces based on our contracts is :
* https://jpg.store
* https://martify.io
* https://filthyrichhorses.com/marketplace
