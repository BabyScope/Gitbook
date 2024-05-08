---
description: >-
  Axelar delivers secure cross-chain communication for Web3, enabling you to build Interchain dApps that grow beyond a single chain. Secure means Axelar is built on proof-of-stake, the battle-tested approach used by Ethereum, Polygon, Cosmos, and more.
---

# Installation

<figure><img src="https://github.com/BabyScope/Gitbook/blob/main/images/axelar.png?raw=true" alt=""><figcaption></figcaption></figure>

**Chain ID**: axelar-dojo-1 | **Latest Version Tag**: v0.35.5 | **Custom Port**: 141  | **WASM**: ON

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
rm -rf axelar-core
git clone https://github.com/axelarnetwork/axelar-core.git
cd axelar-core
git checkout v0.35.5

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.axelar/cosmovisor/genesis/bin
mv bin/axelard $HOME/.axelar/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.axelar/cosmovisor/genesis $HOME/.axelar/cosmovisor/current -f
sudo ln -s $HOME/.axelar/cosmovisor/current/bin/axelard /usr/local/bin/axelard -f

```

### Install Cosmovisor and create a service

```bash
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/axelar.service > /dev/null << EOF
[Unit]
Description=axelar node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.axelar"
Environment="DAEMON_NAME=axelard"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.axelar/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable axelard

```

### Initialize the node

```bash
# Set node configuration
axelard config chain-id axelar-dojo-1
axelard config keyring-backend file
axelard config node tcp://localhost:14157

# Initialize the node
axelard init $MONIKER --chain-id axelar-dojo-1

# Download genesis and addrbook
curl -Ls https://snapshots.kjnodes.com/axelar/genesis.json > $HOME/.axelar/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/axelar/addrbook.json > $HOME/.axelar/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"400f3d9e30b69e78a7fb891f60d76fa3c73f0ecc@axelar.rpc.kjnodes.com:16559\"|" $HOME/.axelar/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.007uaxl\"|" $HOME/.axelar/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.axelar/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:14158\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:14157\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:14160\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:14156\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":16566\"%" $HOME/.axelar/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:17\"%address = \"tcp://0.0.0.0:114117\"%; s%^address = \":8080\"%address = \":14180\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:14190\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:14191\"%; s%:8545%:14145%; s%:8546%:14146%; s%:6065%:14165%" $HOME/.axelar/config/app.toml

```

### Download latest chain snapshot

```bash
curl -L https://snapshots.kjnodes.com/axelar/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.axelar
[[ -f $HOME/.axelar/data/upgrade-info.json ]] && cp $HOME/.axelar/data/upgrade-info.json $HOME/.axelar/cosmovisor/genesis/upgrade-info.json
```

### Start service and check the logs

```bash
sudo systemctl start axelard && sudo journalctl -u axelard -f --no-hostname -o cat

```
