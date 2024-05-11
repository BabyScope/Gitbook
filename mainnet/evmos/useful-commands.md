---
description: >-
  Useful set of commands for node operators. From key management to chain
  governance.
---

# Useful commands

<figure><img src="https://github.com/BabyScope/Gitbook/blob/main/images/axelar.png?raw=true" alt=""><figcaption></figcaption></figure>

### 🔑 Key management <a href="#key-management" id="key-management"></a>

#### Add new key

```bash
evmosd keys add wallet
```

#### Recover existing key

```bash
evmosd keys add wallet --recover
```

#### List all keys

```bash
evmosd keys list
```

#### Delete key

```bash
evmosd keys delete wallet
```

#### Export key to the file

```bash
evmosd keys export wallet
```

#### Import key from the file

```bash
evmosd keys import wallet wallet.backup
```

#### Query wallet balance

```bash
evmosd q bank balances $(evmosd keys show wallet -a)
```

## 👷 Validator management

{% hint style="info" %}
Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.
{% endhint %}

#### Create new validator

```bash
evmosd tx staking create-validator \
--amount 1000000uaxl \
--pubkey $(evmosd tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id axelar-dojo-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.0001uaxl \
-y
```

#### Edit existing validator

```bash
evmosd tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id axelar-dojo-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.0001uaxl \
-y
```

#### Unjail validator

```bash
evmosd tx slashing unjail --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Jail reason

```bash
evmosd query slashing signing-info $(evmosd tendermint show-validator)
```

#### List all active validators

```bash
evmosd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### List all inactive validators

```bash
evmosd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

#### View validator details

```bash
evmosd q staking validator $(evmosd keys show wallet --bech val -a)
```

## 💲 Token management

#### Withdraw rewards from all validators

```bash
evmosd tx distribution withdraw-all-rewards --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Withdraw commission and rewards from your validator

```bash
evmosd tx distribution withdraw-rewards $(evmosd keys show wallet --bech val -a) --commission --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Delegate tokens to yourself

```bash
evmosd tx staking delegate $(evmosd keys show wallet --bech val -a) 1000000uaxl --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Delegate tokens to validator

```bash
evmosd tx staking delegate <TO_VALOPER_ADDRESS> 1000000uaxl --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Redelegate tokens to another validator

```bash
evmosd tx staking redelegate $(evmosd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uaxl --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Unbond tokens from your validator

```bash
evmosd tx staking unbond $(evmosd keys show wallet --bech val -a) 1000000uaxl --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Send tokens to the wallet

```bash
evmosd tx bank send wallet <TO_WALLET_ADDRESS> 1000000uaxl --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

## 🗳 Governance

#### List all proposals

```bash
evmosd query gov proposals
```

#### View proposal by id

```bash
evmosd query gov proposal 1
```

#### Vote 'Yes'

```bash
evmosd tx gov vote 1 yes --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Vote 'No'

```bash
evmosd tx gov vote 1 no --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Vote 'Abstain'

```bash
evmosd tx gov vote 1 abstain --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

#### Vote 'NoWithVeto'

```bash
evmosd tx gov vote 1 NoWithVeto --from wallet --chain-id galileo-3 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uaxl -y
```

## ⚡️ Utility

#### Update ports

```bash
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.evmosd/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.evmosd/config/app.toml
```

#### Update Indexer

**Disable indexer**

```bash
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.evmosd/config/config.toml
```

**Enable indexer**

```bash
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.evmosd/config/config.toml
```

#### Update pruning

```bash
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.evmosd/config/app.toml
```

## 🚨 Maintenance

#### Get validator info

```bash
evmosd status 2>&1 | jq .ValidatorInfo
```

#### Get sync info

```bash
evmosd status 2>&1 | jq .SyncInfo
```

#### Get node peer

```bash
echo $(evmosd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.evmosd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

#### Check if validator key is correct

```bash
[[ $(evmosd q staking validator $(evmosd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(evmosd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

#### Get live peers

```bash
curl -sS http://localhost:14757/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

#### Set minimum gas price

```bash
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0001uaxl\"/" $HOME/.evmosd/config/app.toml
```

#### Enable prometheus

```bash
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.evmosd/config/config.toml
```

#### Reset chain data

```bash
evmosd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.evmosd --keep-addr-book
```

#### Remove node

{% hint style="danger" %}
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!
{% endhint %}

```bash
cd $HOME
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmosd.service
sudo systemctl daemon-reload
rm -f $(which evmosd)
rm -rf $HOME/.evmosd
rm -rf $HOME/evmosd
```

## ⚙️ Service Management

#### Reload service configuration

```bash
sudo systemctl daemon-reload
```

#### Enable service

```bash
sudo systemctl enable evmosd
```

#### Disable service

```bash
sudo systemctl disable evmosd
```

#### Start service

```bash
sudo systemctl start evmosd
```

#### Stop service

```bash
sudo systemctl stop evmosd
```

#### Restart service

```bash
sudo systemctl restart evmosd
```

#### Check service status

```bash
sudo systemctl status evmosd
```

#### Check service logs

```bash
sudo journalctl -u evmosd -f --no-hostname -o cat
```
