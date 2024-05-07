# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u blockxd -f
```

Start service

```bash
sudo systemctl start blockxd
```

Stop service

```bash
sudo systemctl stop blockxd
```

Restart service

```bash
sudo systemctl restart blockxd
```

Check service status

```bash
sudo systemctl status blockxd
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable blockxd
```

Disable Service

```bash
sudo systemctl disable blockxd
```

Sync info

```bash
blockxd status 2>&1 | jq .SyncInfo
```

Node info

```bash
blockxd status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(blockxd tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.blockxd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
blockxd keys add $WALLET
```

Restore executing wallet

```bash
blockxd keys add $WALLET --recover
```

List All Wallets

```bash
blockxd keys list
```

Delete wallet

```bash
blockxd keys delete $WALLET
```

Check Balance

```bash
blockxd q bank balances $(blockxd keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
blockxd keys export $WALLET
```

View EVM Prived Key

```bash
blockxd keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
blockxd keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, abcx

Withdraw all rewards

```bash
blockxd tx distribution withdraw-all-rewards --from $WALLET --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx
```

Withdraw rewards and commission from your validator

```bash
blockxd tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx -y
```

Check your balance

```bash
blockxd query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
blockxd tx staking delegate $(blockxd keys show $WALLET --bech val -a) 1000000abcx --from $WALLET --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx -y
```

Delegate

```bash
blockxd tx staking delegate <TO_VALOPER_ADDRESS> 1000000abcx --from $WALLET --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx -y
```

Redelegate Stake to Another Validator

```bash
blockxd tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000abcx --from $WALLET --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx -y
```

Unbond

```bash
blockxd tx staking unbond $(blockxd keys show $WALLET --bech val -a) 1000000abcx --from $WALLET --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx -y
```

Transfer Funds

```bash
blockxd tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000abcx --gas=300000 --gas-prices=1000000000abcx -y
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

MonikerIdentityDetailsAmount, abcxCommission rateCommission max rateCommission max change rate

Create New Validator

```bash
blockxd tx staking create-validator \
--amount 1000000abcx \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(blockxd tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain 💚" \
--chain-id blockx_100-1 \
--gas=300000 --gas-prices=1000000000abcx \
-y
```

Edit Existing Validator

```bash
blockxd tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain 💚" \
--from $WALLET \
--chain-id blockx_100-1 \
--gas=300000 --gas-prices=1000000000abcx \
-y
```

Validator info

```bash
blockxd status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
blockxd q staking validator $(blockxd keys show $WALLET --bech val -a)
```

Jailing info

```bash
blockxd q slashing signing-info $(blockxd tendermint show-validator)
```

Slashing parameters

```bash
blockxd q slashing params
```

Unjail validator

```bash
blockxd tx slashing unjail --from $WALLET --chain-id blockx_100-1 --gas=300000 --gas-prices=1000000000abcx -y
```

Active Validators List

```bash
blockxd q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(blockxd q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(blockxd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
blockxd q slashing signing-info $(blockxd tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

TitleDescriptionDeposit, abcx

Create New Text Proposal

```bash
blockxd  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000abcx \
--type Text \
--from $WALLET \
--gas=300000 --gas-prices=1000000000abcx \
-y 
```

Proposals List

```bash
blockxd query gov proposals
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
blockxd query gov proposal 1
```

Vote

```bash
blockxd tx gov vote 1 yes --from $WALLET --chain-id blockx_100-1  --gas=300000 --gas-prices=1000000000abcx -y
```
