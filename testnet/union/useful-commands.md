# Useful commands

![](<../../.gitbook/assets/union (1).png>)

### 🔑 Key management <a href="#key-management" id="key-management"></a>

**ADD NEW KEY**

```
uniond keys add wallet
```

**RECOVER EXISTING KEY**

```
uniond keys add wallet --recover
```

**LIST ALL KEYS**

```
uniond keys list
```

**DELETE KEY**

```
uniond keys delete wallet
```

**EXPORT KEY TO A FILE**

```
uniond keys export wallet
```

**IMPORT KEY FROM A FILE**

```
uniond keys import wallet wallet.backup
```

**QUERY WALLET BALANCE**

```
uniond q bank balances $(uniond keys show wallet -a)
```

### 👷 Validator management <a href="#validator-management" id="validator-management"></a>

Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

**CREATE NEW VALIDATOR**

```
uniond tx staking create-validator <(cat <<EOF
{
  "pubkey": $(uniond comet show-validator),
  "amount": "1000000muno",
  "moniker": "YOUR_MONIKER_NAME",
  "identity": "YOUR_KEYBASE_ID",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_SECURITY_EMAIL",
  "details": "YOUR_DETAILS",
  "commission-rate": "0.05",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id union-testnet-8 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0muno \
-y

```

**EDIT EXISTING VALIDATOR**

```
uniond tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id union-testnet-8 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0muno \
-y

```

**UNJAIL VALIDATOR**

```
uniond tx slashing unjail --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**JAIL REASON**

```
uniond query slashing signing-info $(uniond tendermint show-validator)
```

**LIST ALL ACTIVE VALIDATORS**

```
uniond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**LIST ALL INACTIVE VALIDATORS**

```
uniond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

**VIEW VALIDATOR DETAILS**

```
uniond q staking validator $(uniond keys show wallet --bech val -a)
```

### 💲 Token management <a href="#token-management" id="token-management"></a>

**WITHDRAW REWARDS FROM ALL VALIDATORS**

```
uniond tx distribution withdraw-all-rewards --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR**

```
uniond tx distribution withdraw-rewards $(uniond keys show wallet --bech val -a) --commission --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**DELEGATE TOKENS TO YOURSELF**

```
uniond tx staking delegate $(uniond keys show wallet --bech val -a) 1000000muno --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**DELEGATE TOKENS TO VALIDATOR**

```
uniond tx staking delegate <TO_VALOPER_ADDRESS> 1000000muno --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**REDELEGATE TOKENS TO ANOTHER VALIDATOR**

```
uniond tx staking redelegate $(uniond keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000muno --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**UNBOND TOKENS FROM YOUR VALIDATOR**

```
uniond tx staking unbond $(uniond keys show wallet --bech val -a) 1000000muno --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**SEND TOKENS TO THE WALLET**

```
uniond tx bank send wallet <TO_WALLET_ADDRESS> 1000000muno --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

### 🗳 Governance <a href="#governance" id="governance"></a>

**LIST ALL PROPOSALS**

```
uniond query gov proposals
```

**VIEW PROPOSAL BY ID**

```
uniond query gov proposal 1
```

**VOTE ‘YES’**

```
uniond tx gov vote 1 yes --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**VOTE ‘NO’**

```
uniond tx gov vote 1 no --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**VOTE ‘ABSTAIN’**

```
uniond tx gov vote 1 abstain --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

**VOTE ‘NOWITHVETO’**

```
uniond tx gov vote 1 NoWithVeto --from wallet --chain-id union-testnet-9 --gas-adjustment 1.4 --gas auto --gas-prices 0.00025muno -y
```

### ⚡️ Utility <a href="#utility" id="utility"></a>

**UPDATE PORTS**

```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.union/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.union/config/app.toml
```

**UPDATE INDEXER**

**Disable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.union/config/config.toml
```

**Enable indexer**

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.union/config/config.toml
```

**UPDATE PRUNING**

```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.union/config/app.toml
```

### 🚨 Maintenance <a href="#maintenance" id="maintenance"></a>

**GET VALIDATOR INFO**

```
uniond status 2>&1 | jq .ValidatorInfo
```

**GET SYNC INFO**

```
uniond status 2>&1 | jq .SyncInfo
```

**GET NODE PEER**

```
echo $(uniond tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.union/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

**CHECK IF VALIDATOR KEY IS CORRECT**

```
[[ $(uniond q staking validator $(uniond keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(uniond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

**GET LIVE PEERS**

```
curl -sS http://localhost:15357/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

**SET MINIMUM GAS PRICE**

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00025uuno,0.0018ibc/2180E84E20F5679FCC760D8C165B60F42065DEF7F46A72B447CFF1B7DC6C0A65,0.00025ibc/E2D2F6ADCC68AA3384B2F5DFACCA437923D137C14E86FB8A10207CF3BED0C8D4\"/" $HOME/.union/config/app.toml
```

**ENABLE PROMETHEUS**

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.union/config/config.toml
```

**RESET CHAIN DATA**

```
uniond tendermint unsafe-reset-all --keep-addr-book --home $HOME/.union--keep-addr-book
```

**REMOVE NODE**

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!

```
cd $HOME
sudo systemctl stop union-testnet.service
sudo systemctl disable union-testnet.service
sudo rm /etc/systemd/system/union-testnet.service
sudo systemctl daemon-reload
rm -f $(which uniond)
rm -rf $HOME/.union
```

### ⚙️ Service Management <a href="#service-management" id="service-management"></a>

**RELOAD SERVICE CONFIGURATION**

```
sudo systemctl daemon-reload
```

**ENABLE SERVICE**

```
sudo systemctl enable union-testnet.service
```

**DISABLE SERVICE**

```
sudo systemctl disable union-testnet.service
```

**START SERVICE**

```
sudo systemctl start union-testnet.service
```

**STOP SERVICE**

```
sudo systemctl stop union-testnet.service
```

**RESTART SERVICE**

```
sudo systemctl restart union-testnet.service
```

**CHECK SERVICE STATUS**

```
sudo systemctl status union-testnet.service
```

**CHECK SERVICE LOGS**

```
sudo journalctl -u union-testnet.service -f --no-hostname -o cat
```
