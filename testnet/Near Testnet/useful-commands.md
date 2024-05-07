# Useful Commands

Official docs: [https://docs.namada.net/users/transparent-accounts](https://docs.namada.net/users/transparent-accounts)\
Faucet: [https://faucet.heliax.click/](https://faucet.heliax.click/)\


### Wallet operations <a href="#wallet-operations" id="wallet-operations"></a>

create a new keypair:

```bash
namadaw gen --alias $WALLET
```

restore existing wallet:

```bash
namada wallet derive --alias $WALLET --hd-path default
```

find your wallet address:

```bash
namadaw find --alias $WALLET
```

add some tokens using faucet:

```bash
https://faucet.heliax.click/
```

check balance:

```bash
namadac balance --owner $WALLET
```

check all known keys and addresses in the wallet:

```bash
namadaw list
```

send payment from one address to another:

```bash
namadac transfer --source $WALLET --target ${WALLET}1 --token NAAN --amount 1 --signing-keys $WALLET --memo $MEMO
```

remove keys:

```bash
namadaw remove --alias $WALLET --do-it
```

### Staking <a href="#staking" id="staking"></a>

```
If you find this guide helpful, you can stake to our validator. 
We would really appreciate it! 🚀
```

add a variable with the validator address:

```bash
VAL_ADDRESS="tnam1q8vhe85yc5ttt7mveds58gyqzavfwnn9eqhfxpgc" # address of validator you want to stake to
```

export the variable:

```bash
echo "export VAL_ADDRESS="$VAL_ADDRESS"" >> $HOME/.bash_profile \
source $HOME/.bash_profile
```

delegate tokens:

```bash
namadac bond --source $WALLET --validator $VAL_ADDRESS --amount 500 --node tcp://namada-testnet-tcprpc.itrocket.net:33657 --memo $MEMO
```

check your user bonds:

```bash
namadac bonds --owner $WALLET --node tcp://namada-testnet-tcprpc.itrocket.net:33657
```

check all bonded nodes:

```bash
namadac bonded-stake --node tcp://namada-testnet-tcprpc.itrocket.net:33657
```

add stake:

```bash
namadac bond --source $WALLET --validator $VAL_ADDRESS --amount 500 --node tcp://namada-testnet-tcprpc.itrocket.net:33657 --memo $MEMO
```

unbond the tokens:

```bash
namadac unbond --source $WALLET --validator $VAL_ADDRESS --amount 15 --node tcp://namada-testnet-tcprpc.itrocket.net:33657 --memo $MEMO
```

wait for 6 epochs, then check when the unbonded tokens can be withdrawed:

```bash
namadac bonds --owner $WALLET --node tcp://namada-testnet-tcprpc.itrocket.net:33657
```

withdraw unbonded tokens:

```bash
namadac withdraw --source $WALLET --validator $VAL_ADDRESS --node tcp://namada-testnet-tcprpc.itrocket.net:33657 --memo $MEMO
```

redelegate:

```bash
namadac redelegate --owner $WALLET --source-validator $VAL_ADDRESS --destination-validator <destination-validator-address> --amount 10 --node tcp://namada-testnet-tcprpc.itrocket.net:33657 --memo $MEMO
```

claim rewards:

```bash
namadac claim-rewards --source $WALLET --validator $VAL_ADDRESS --node tcp://namada-testnet-tcprpc.itrocket.net:33657 --memo $MEMO
```

query the pending reward tokens without claiming:

```bash
namadac rewards --source $WALLET --validator $VAL_ADDRESS --node tcp://namada-testnet-tcprpc.itrocket.net:33657
```

### Multisign <a href="#multisign" id="multisign"></a>

generate key\_1:

```bash
namadaw gen --alias $WALLET
```

generate key\_2 and etc:

```bash
namadaw gen --alias ${WALLET}1
```

chech your public key:

```bash
namadaw find --alias $WALLET | awk '/Public key:/ {print $3}'
```

init non-multisig account (single signer):

```bash
namadac init-account --alias ${WALLET}-multisig --public-keys $WALLET --signing-keys $WALLET --memo $MEMO
```

init multisig account (at least 2 signers):

```bash
namadac init-account --alias ${WALLET}1-multisig --public-keys $WALLET,${WALLET}1 --signing-keys $WALLET,${WALLET}1 --threshold 2 --memo $MEMO
```

create a folder for a transaction:

```bash
mkdir tx_dumps
```

create transaction:

```bash
namadac transfer --source ${WALLET}1-multisig --target ${WALLET}1 --token NAAN --amount 10 --signing-keys $WALLET,${WALLET}1 --dump-tx --output-folder-path tx_dumps --memo $MEMO
```

sign the transaction:

```bash
namadac sign-tx --tx-path "<path-to-.tx-file>" --signing-keys $WALLET,${WALLET}1 --owner ${WALLET}1-multisig --memo $MEMO
```

save as a variable offline\_signature 1:

```bash
export SIGNATURE_ONE="<signature-file-name>"
```

save as a variable offline\_signature 2:

```bash
export SIGNATURE_TWO="<signature-2-file-name>"
```

submit transaction:

```bash
namadac tx --tx-path "<path-to-.tx-file>" --signatures $SIGNATURE_ONE,$SIGNATURE_TWO --owner ${WALLET}1-multisig --gas-payer $WALLET --memo $MEMO
```

changing the multisig threshold:

```bash
namadac update-account --address ${WALLET}1-multisig --threshold 1 --signing-keys $WALLET,${WALLET}1 --memo $MEMO
```

check that the threshold has been updated correctly by running:

```bash
namadac query-account --owner ${WALLET}1-multisig
```

changing the public keys of a multisig account:

```bash
namadac update-account --address ${WALLET}1-multisig --public-keys ${WALLET}2,${WALLET}3,${WALLET}4 --signing-keys $WALLET,${WALLET}1 --memo $MEMO
```

initialize an established account:

```bash
namadac init-account --alias ${WALLET}1-multisig --public-keys ${WALLET}2,${WALLET}3,${WALLET}4  --signing-keys $WALLET,${WALLET}1  --threshold 1 --memo $MEMO
```

### MASP <a href="#masp" id="masp"></a>

randomly generate a new spending key:

```bash
namadaw gen --shielded --alias ${WALLET}-shielded
```

create a new payment address:

```bash
namadaw gen-payment-addr --key ${WALLET}-shielded --alias ${WALLET}-shielded-addr
```

send a shielding transfer:

```bash
namadac transfer --source $WALLET --target ${WALLET}-shielded-addr --token NAAN --amount 10 --memo $MEMO
```

view balance:

```bash
namadac balance --owner ${WALLET}-shielded
```

generate another spending key:

```bash
namadaw gen --shielded --alias ${WALLET}1-shielded
```

create a payment address:

```bash
namadaw gen-payment-addr --key ${WALLET}1-shielded --alias ${WALLET}1-shielded-addr
```

shielded transfers (once the user has a shielded balance, it can be transferred to another shielded address):

```bash
namadac transfer  --source ${WALLET}-shielded --target ${WALLET}1-shielded-addr --token NAAN --amount 4 --signing-keys <your-implicit-account-alias> --memo $MEMO
```

unshielding transfers (from a shielded to a transparent account):

```bash
namadac transfer --source ${WALLET}-shielded --target $WALLET --token NAAN --amount 4 --signing-keys <your-implicit-account-alias> --memo $MEMO
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

check sync status and node info:

```bash
curl http://127.0.0.1:26657/status | jq
```

check balance:

```bash
namadac balance --owner $ALIAS
```

check keys:

```bash
namadaw list
```

find your validator address:

```bash
namadac find-validator --tm-address=$(curl -s localhost:26657/status | jq -r .result.validator_info.address) --node localhost:26657
```

stake funds:

```bash
namadac bond --source $WALLET --validator $VALIDATOR_ADDRESS --amount 10 --memo $MEMO
```

self-bonding:

```bash
namadac bond --validator $VALIDATOR_ADDRESS --amount 10 --memo $MEMO
```

check your validator bond status:

```bash
namadac bonds --validator $VALIDATOR_ADDRESS
```

check your user bonds:

```bash
namadac bonds --owner $WALLET
```

check all bonded nodes:

```bash
namadac bonded-stake
```

find all the slashes:

```bash
namadac slashes
```

non-self unbonding (validator alias can be used instead of address):

```bash
namadac unbond --source $WALLET --validator $VALIDATOR_ADDRESS --amount 1.5 --memo $MEMO
```

self-unbonding:

```bash
namadac unbond --validator $VALIDATOR_ADDRESS --amount 1.5 --memo $MEMO
```

withdrawing unbonded tokens (available 6 epochs after unbonding):

```bash
namadac withdraw --source $WALLET --validator $VALIDATOR_ADDRESS --memo $MEMO
```

find your validator status:

```bash
namadac validator-state --validator $VALIDATOR_ADDRESS
```

check epoch:

```bash
namada client epoch
```

unjail, you need to wait 2 epochs:

```bash
namada client unjail-validator --validator $VALIDATOR_ADDRESS --node http://127.0.0.1:26657 --memo $MEMO
```

deactivate validator:

```bash
namadac deactivate-validator --validator $VALIDATOR_ADDRESS --memo $MEMO
```

reactivate validator:

```bash
namadac reactivate-validator --validator $VALIDATOR_ADDRESS --memo $MEMO
```

Change consensus key:

```bash
namadac change-consensus-key --validator $VALIDATOR_ADDRESS --memo $MEMO --signing-keys $WALLET --node http://127.0.0.1:26657
```

Generate priv\_validator\_key:

```bash
namadaw convert --alias <consensus_key_name>
```

change validator commission rate:

```bash
namadac change-commission-rate --validator $VALIDATOR_ADDRESS --commission-rate <commission-rate> --memo $MEMO
```

change validator metadata:

```bash
namadac change-metadata --validator $VALIDATOR_ADDRESS --memo $MEMO
```

### Governance <a href="#governance" id="governance"></a>

all proposals list:

```bash
namadac query-proposal
```

edit proposal:

```bash
namadac query-proposal --proposal-id <PROPOSAL_ID>
```

save wallet address:

```bash
WALLET_ADDRESS=$(namadaw find --alias $WALLET | grep "Implicit" | awk '{print $3}')
```

import the variable into system:

```bash
echo "export WALLET_ADDRESS="$WALLET_ADDRESS"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

vote:

```bash
namadac vote-proposal --proposal-id <proposal-id> --vote yay --address $WALLET_ADDRESS --signing-keys $WALLET --memo $MEMO
```

### Sync and Consensus <a href="#sync-and-consensus" id="sync-and-consensus"></a>

check logs:

```bash
sudo journalctl -u namadad -f
```

check sync status and node info:

```bash
curl http://127.0.0.1:26657/status | jq
```

check consensus state:

```bash
curl -s localhost:26657/consensus_state | jq .result.round_state.height_vote_set[0].prevotes_bit_array
```

full consensus state:

```bash
curl -s localhost:12657/dump_consensus_state
```

your validator votes (prevote):

```bash
curl -s http://localhost:26657/dump_consensus_state | jq '.result.round_state.votes[0].prevotes' | grep $(curl -s ht
```
