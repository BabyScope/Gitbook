---
description: >-
  Evmos is a proof-of-stake (PoS) network that enables value transfer between
  Ethereum and Cosmos ecosystems. It is developed using the Cosmos software
  development kit (SDK).
---

# Installation

<figure><img src="../../images/evmos.png" alt=""><figcaption></figcaption></figure>

**Chain ID**: evmos\_9001-2 | **Latest Version Tag**: v18.0.0 | **Custom Port**: 169

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

{% hint style="info" %}
Replace **YOUR\_MONIKER\_GOES\_HERE** with your validator name
{% endhint %}

```bash
MONIKER="YOUR_MONIKER_GOES_HERE"
```

### Install dependencies

#### Update system and install build tools

```bash
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

#### Install Go

```bash
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.2.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

### Download and build binaries

```bash
# Clone project repository
cd $HOME
rm -rf evmos
git clone https://github.com/evmos/evmos.git
cd evmos
git checkout v18.0.0

go mod edit -replace github.com/tendermint/tm-db=github.com/notional-labs/tm-db@v0.6.8-pebble
go mod tidy
go mod edit -replace github.com/cometbft/cometbft-db=github.com/notional-labs/cometbft-db@pebble
go mod tidy

go install -ldflags "-w -s -X github.com/cosmos/cosmos-sdk/types.DBBackend=pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Version=$(git describe --tags)-pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Commit=$(git log -1 --format='%H')" -tags pebbledb ./...
```

### Install Cosmovisor and create a service

```bash
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/evmos.service > /dev/null << EOF
[Unit]
Description=evmos node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.evmosd"
Environment="DAEMON_NAME=evmosd"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.evmosd/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable evmos.service

```

### Initialize the node

```bash
# Set node configuration
evmosd config chain-id evmos_9001-2
evmosd config keyring-backend file
evmosd config node tcp://localhost:26657


evmosd init $MONIKER --chain-id evmos_9001-2

# Download genesis and addrbook
curl -Ls http://snapshots.stakevillage.net/snapshots/evmos_9001-2/genesis.json > $HOME/.evmosd/config/genesis.json
curl -Ls http://snapshots.stakevillage.net/snapshots/evmos_9001-2/addrbook.json > $HOME/.evmosd/config/addrbook.json

# Add seeds
sed -i 's/seeds = ""/seeds = "a56f27699b7e47ce79335509c0863bcfe6ae1347@rpc.evmos.nodestake.top:666"/' ~/.evmosd/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"400000000000aevmos\"|" $HOME/.evmosd/config/app.toml

# prunning
pruning="custom"
pruning_keep_recent="50000"
pruning_keep_every="0"
pruning_interval="19"

sed -i "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.evmosd/config/app.toml
sed -i "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.evmosd/config/app.toml
sed -i "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.evmosd/config/app.toml
sed -i "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.evmosd/config/app.toml

# snapshots
sed -i 's/snapshot-interval *=.*/snapshot-interval = 0/' $HOME/.evmosd/config/app.toml

# set pebbledb
db_backend="pebbledb"
sed -i "s/^db_backend *=.*/db_backend = \"$db_backend\"/" $HOME/.evmosd/config/config.toml
sed -i "s/^app-db-backend *=.*/app-db-backend = \"$db_backend\"/" $HOME/.evmosd/config/app.toml

sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.evmosd/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"localhost:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%; s%^address = \"127.0.0.1:8545\"%address = \"0.0.0.0:${CUSTOM_PORT}45\"%; s%^ws-address = \"127.0.0.1:8546\"%ws-address = \"0.0.0.0:${CUSTOM_PORT}46\"%" $HOME/.evmosd/config/app.toml


evmosd config node tcp://localhost:${CUSTOM_PORT}57
```

### Download latest chain snapshot

```bash
Download snapshot
```

### Start service and check the logs

```bash
sudo systemctl start evmos.service && sudo journalctl -u evmos.service -f --no-hostname -o cat

```
