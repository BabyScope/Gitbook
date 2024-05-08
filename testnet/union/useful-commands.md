# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u sided -f
```

Start service

```bash
sudo systemctl start sided
```

Stop service

```bash
sudo systemctl stop sided
```

Restart service

```bash
sudo systemctl restart sided
```

Check service status

```bash
sudo systemctl status sided
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable sided
```

Disable Service

```bash
sudo systemctl disable sided
```

Sync info

```bash
sided status 2>&1 | jq .SyncInfo
```

Node info

```bash
sided status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(sided tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.side/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
sided keys add $WALLET
```

Restore executing wallet

```bash
sided keys add $WALLET --recover
```

List All Wallets

```bash
sided keys list
```

Delete wallet

```bash
sided keys delete $WALLET
```

Check Balance

```bash
sided q bank balances $(sided keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
sided keys export $WALLET
```

View EVM Prived Key

```bash
sided keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
sided keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, uside

Withdraw all rewards

```bash
sided tx distribution withdraw-all-rewards --from $WALLET --chain-id side-testnet-3 --gas auto --fees 1000uside
```

Withdraw rewards and commission from your validator

```bash
sided tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id side-testnet-3 --gas auto --fees 1000uside -y
```

Check your balance

```bash
sided query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
sided tx staking delegate $(sided keys show $WALLET --bech val -a) 1000000uside --from $WALLET --chain-id side-testnet-3 --gas auto --fees 1000uside -y
```

Delegate

```bash
sided tx staking delegate <TO_VALOPER_ADDRESS> 1000000uside --from $WALLET --chain-id side-testnet-3 --gas auto --fees 1000uside -y
```

Redelegate Stake to Another Validator

```bash
sided tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000uside --from $WALLET --chain-id side-testnet-3 --gas auto --fees 1000uside -y
```

Unbond

```bash
sided tx staking unbond $(sided keys show $WALLET --bech val -a) 1000000uside --from $WALLET --chain-id side-testnet-3 --gas auto --fees 1000uside -y
```

Transfer Funds

```bash
sided tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000uside --gas auto --fees 1000uside -y
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

MonikerIdentityDetailsAmount, usideCommission rateCommission max rateCommission max change rate

Create New Validator

```bash
sided tx staking create-validator \
--amount 1000000uside \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(sided tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id side-testnet-3 \
--gas auto --fees 1000uside \
-y
```

Edit Existing Validator

```bash
sided tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id side-testnet-3 \
--gas auto --fees 1000uside \
-y
```

Validator info

```bash
sided status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
sided q staking validator $(sided keys show $WALLET --bech val -a)
```

Jailing info

```bash
sided q slashing signing-info $(sided tendermint show-validator)
```

Slashing parameters

```bash
sided q slashing params
```

Unjail validator

```bash
sided tx slashing unjail --from $WALLET --chain-id side-testnet-3 --gas auto --fees 1000uside -y
```

Active Validators List

```bash
sided q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(sided q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(sided status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
sided q slashing signing-info $(sided tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

TitleDescriptionDeposit, uside

Create New Text Proposal

```bash
sided  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000uside \
--type Text \
--from $WALLET \
--gas auto --fees 1000uside \
-y 
```

Proposals List

```bash
sided query gov proposals
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
sided query gov proposal 1
```

Vote

```bash
sided tx gov vote 1 yes --from $WALLET --chain-id side-testnet-3  --gas auto --fees 1000uside -y
```
