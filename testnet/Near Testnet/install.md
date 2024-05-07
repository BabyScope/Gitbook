# Install

### Manual Installation <a href="#installation" id="installation"></a>

[Official Documentation](https://docs.namada.net/testnets/environment-setup.html)Recommended Hardware: CPU: x86\_64 or arm64, 8GB DDR4, 1TB of storage

Update packages and Install dependencies select 1

```bash
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler
```

Install:

```bash
cd $HOME
! [ -x "$(command -v go)" ] && {
VER="1.20.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
}
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

Replace your Validator and Wallet name, save and import variables into system. Change default port if needed.

```bash
NAMADA_PORT=26
echo "export NAMADA_PORT="$NAMADA_PORT"" >> $HOME/.bash_profile
echo "export ALIAS="CHOOSE_A_NAME_FOR_YOUR_VALIDATOR"" >> $HOME/.bash_profile
echo "export MEMO="CHOOSE_YOUR_tpknam_ADDRESS"" >> $HOME/.bash_profile
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export PUBLIC_IP=$(wget -qO- eth0.me)" >> $HOME/.bash_profile
echo "export TM_HASH="v0.1.4-abciplus"" >> $HOME/.bash_profile
echo "export CHAIN_ID="shielded-expedition.88f17d1d14"" >> $HOME/.bash_profile
echo "export BASE_DIR="$HOME/.local/share/namada"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Install Rust:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env
```

Install CometBFT:

```bash
cd $HOME
rm -rf cometbft
git clone https://github.com/cometbft/cometbft.git
cd cometbft
git checkout v0.37.2
make build
sudo cp $HOME/cometbft/build/cometbft /usr/local/bin/
cometbft version
```

Download and build Namada binaries:

```bash
cd $HOME
rm -rf namada
git clone https://github.com/anoma/namada
cd namada
wget https://github.com/anoma/namada/releases/download/v0.31.6/namada-v0.31.6-Linux-x86_64.tar.gz
tar -xvf namada-v0.31.6-Linux-x86_64.tar.gz
rm namada-v0.31.6-Linux-x86_64.tar.gz
cd namada-v0.31.6-Linux-x86_64
sudo mv namad* /usr/local/bin/
if [ ! -d "$BASE_DIR" ]; then
    mkdir -p "$BASE_DIR"
fi
```

Check Namada version:

```bash
namada --version
```

🔗 Join-network as Pre-Genesis Validator

📁 Move your pre-genesis folder to _$BASE\_DIR_ and join the network:

```bash
cd $HOME
cp -r ~/.namada/pre-genesis $BASE_DIR/
namada client utils join-network --chain-id $CHAIN_ID --genesis-validator $ALIAS
```

Join-network as Full Nodes or Post-Genesis Validator:

```bash
namada client utils join-network --chain-id $CHAIN_ID
```

Create Service file:

```bash
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$BASE_DIR
Environment=TM_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
ExecStart=$(which namada) node ledger run
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

Set custom ports in config.toml:

```bash
sed -i.bak -e "s%:26658%:${NAMADA_PORT}658%g;
s%:26657%:${NAMADA_PORT}657%g;
s%:26656%:${NAMADA_PORT}656%g;
s%:26545%:${NAMADA_PORT}545%g;
s%:8545%:${NAMADA_PORT}545%g;
s%:26660%:${NAMADA_PORT}660%g" $HOME/.local/share/namada/shielded-expedition.88f17d1d14/config.toml
```

Enable and start service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl restart namadad && sudo journalctl -u namadad -f
```

🔎 Create and fund walletCreate wallet:

```bash
namadaw gen --alias $WALLET
```

Restore existing wallet:

```bash
namadaw derive --alias $WALLET
```

Find your wallet address:

```bash
namadaw find --alias $WALLET
```

Copy the implicit address (starts with tnam...) for the next stepFund your wallet from [faucet](https://faucet.housefire.luminara.icu/)After a couple of minutes, check the balance

```bash
namadac balance --owner $WALLET
```

List known keys and addresses in the wallet

```bash
namadaw list
```

Delete wallet

```bash
namadaw remove --alias $WALLET --do-it
```

Check Sync status, once your node is fully synced, the output from above will sayfalse

```bash
curl http://127.0.0.1:26657/status | jq 
```

🧑‍🎓 Turn your full node into a validatorInitiate a validator

```bash
namadac init-validator \
		--commission-rate 0.07 \
		--max-commission-rate-change 1 \
		--signing-keys $WALLET \
		--alias $ALIAS \
		--email <EMAIL_ADDRESS> \
		--website <WEBSITE> \ 
		--discord-handle <DISCORD> \
		--account-keys $WALLET \
		--memo $MEMO
```

Find your establishedvalidator address

```bash
namadaw list | grep -A 1 ""$ALIAS"" | grep "Established"
```

Replace your Validator address, save and import variables into system

```bash
VALIDATOR_ADDRESS=$(namadaw list | grep -A 1 "\"$ALIAS\"" | grep "Established" | awk '{print $3}') 
echo "export VALIDATOR_ADDRESS="$VALIDATOR_ADDRESS"" >> $HOME/.bash_profile 
source $HOME/.bash_profile
```

Restart the node and wait for 2 epochs

```bash
sudo systemctl restart namadad && sudo journalctl -u namadad -f
```

Check epoch

```bash
namada client epoch
```

Delegate tokens

```bash
namadac bond --validator $ALIAS --source $WALLET --amount 1000 --memo $MEMO
```

Wait for 3 epochs and check validator is in the consensus set

```bash
namadac validator-state --validator $ALIAS
```

Check your validator bond status

```bash
namada client bonds --owner $WALLET
```

Find your validator status

```bash
namada client validator-state --validator $VALIDATOR_ADDRESS
```

Add stake

```bash
namadac bond --source $WALLET --validator $VALIDATOR_ADDRESS --amount 1000
```

Query the set of validators

```bash
namadac bonded-stake
```

Unbond the tokens

```bash
namadac unbond --source $WALLET --validator $VALIDATOR_ADDRESS --amount 1000
```

Wait for 6 epochs, then check when the unbonded tokens can be withdrawed

```bash
namadac bonds --owner $WALLET
```

Withdraw the unbonded tokens

```bash
namadac withdraw --source $WALLET --validator $VALIDATOR_ADDRESS
```

### Security <a href="#security" id="security"></a>

To protect you keys please don\`t share your privkey, mnemonic and follow a basic security rules

#### Set up ssh keys for authentication <a href="#ssh" id="ssh"></a>

You can use this [guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04) to configure ssh authentication and disable password authentication on your server

#### Firewall security <a href="#firewall" id="firewall"></a>

Set the default to allow outgoing connections, deny all incoming, allow ssh and node p2p port

```bash
sudo ufw default allow outgoing 
sudo ufw default deny incoming 
sudo ufw allow ssh/tcp 
sudo ufw allow ${NAMADA_PORT}656/tcp
sudo ufw enable
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop namadad
sudo systemctl disable namadad
sudo rm -rf /etc/systemd/system/namadad.service
sudo systemctl daemon-reload
sudo rm $(which namada)
sudo rm -rf $HOME/.local/share/namada/public-testnet-15.0dacadb8d663
```
