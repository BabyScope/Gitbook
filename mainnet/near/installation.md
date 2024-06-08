---
description: >-
  NEAR is the chain abstraction stack, empowering builders to create apps that
  scale to billions of users and across all blockchains
---

# Installation

<figure><img src="../../images/near.png" alt=""><figcaption></figcaption></figure>

**Chain ID**: mainnet | **Latest Version Tag**: v1.39.2

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

### Install dependencies

#### Update system and install build tools

```bash
sudo apt -q update
sudo apt install -y curl git binutils-dev libcurl4-openssl-dev libudev-dev libssl-dev build-essential zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ docker.io protobuf-compiler libssl-dev pkg-config clang llvm cargo awscli
sudo apt -qy upgrade
```

#### Install Rust

```bash
curl --proto '=https' -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
```

### Download and build binaries

```bash
# Clone project repository
cd $HOME
rm -rf nearcore
git clone https://github.com/near/nearcore
cd nearcore
git fetch origin --tags
git checkout tags/1.39.2 -b mynode
make release
sudo mv $HOME/nearcore/target/release/neard /usr/local/bin/neard
```

### Install Cosmovisor and create a service

```bash
# Create service
sudo tee /etc/systemd/system/neard.service > /dev/null << EOF
[Unit]
Description=Near Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which neard) --home $HOME/.near run
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable neard
```

### Initialize working directory

```bash
neard --home $HOME/.near init --chain-id mainnet --download-genesis --download-config
```

### Replace the config.json

```bash
rm ~/.near/config.json
wget https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/mainnet/config.json -P ~/.near/
```

### Download latest chain snapshot

```bash
aws s3 --no-sign-request cp s3://near-protocol-public/backups/mainnet/rpc/latest .
LATEST=$(cat latest)
aws s3 --no-sign-request cp --no-sign-request --recursive s3://near-protocol-public/backups/mainnet/rpc/$LATEST ~/.near/data
```

### Start service and check the logs

```bash
sudo systemctl start neard && sudo journalctl -u neard -f --no-hostname -o cat
```
