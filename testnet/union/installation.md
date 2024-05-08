# Installation

### Manual Installation <a href="#installation" id="installation"></a>

Official DocumentationRecommended Hardware: 4 Cores, 8GB RAM, 200GB of storage (NVME)

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

Node Test\
Name Wallet\
Port 45

```bash
# install go, if needed
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export SIDE_CHAIN_ID="side-testnet-3"" >> $HOME/.bash_profile
echo "export SIDE_PORT="45"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf side
git clone https://github.com/sideprotocol/side.git
cd side
git checkout v0.7.0
make install

# config and init app
sided config node tcp://localhost:${SIDE_PORT}657
sided config keyring-backend os
sided config chain-id side-testnet-3
sided init "test" --chain-id side-testnet-3

# download genesis and addrbook
wget -O $HOME/.side/config/genesis.json https://testnet-files.itrocket.net/side/genesis.json
wget -O $HOME/.side/config/addrbook.json https://testnet-files.itrocket.net/side/addrbook.json

# set seeds and peers
SEEDS="9c14080752bdfa33f4624f83cd155e2d3976e303@side-testnet-seed.itrocket.net:45656"
PEERS="bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656,6decdc5565bf5232cdf5597a7784bfe828c32277@158.220.126.137:11656,e9ee4fb923d5aab89207df36ce660ff1b882fc72@136.243.33.177:21656,169332e1a5aad8e49fced765992201774a754cd0@95.216.27.29:34656,2a6d31c23160e49db1f03a884dc7b9602fffe895@176.9.126.85:30004,c8962c5fe2622e969399f98377e775147425cf3f@173.249.44.60:34656,ca3379b48e196c3ef910a08452b459b0f327fdb6@95.216.3.115:34656,2780ffa710b0d42dacc4eeffb4c6bc145ef6636f@38.129.16.236:26656,bae861fd068a26b90235b3677d28d4f37d747e44@173.212.253.77:26656,b588e261519d49e436fc503af5b602810110bd36@194.163.149.7:26656,e52da5e5fecf65abf9d7a3135196240f065deed3@207.180.212.200:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.side/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${SIDE_PORT}317%g;
s%:8080%:${SIDE_PORT}080%g;
s%:9090%:${SIDE_PORT}090%g;
s%:9091%:${SIDE_PORT}091%g;
s%:8545%:${SIDE_PORT}545%g;
s%:8546%:${SIDE_PORT}546%g;
s%:6065%:${SIDE_PORT}065%g" $HOME/.side/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${SIDE_PORT}658%g;
s%:26657%:${SIDE_PORT}657%g;
s%:6060%:${SIDE_PORT}060%g;
s%:26656%:${SIDE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${SIDE_PORT}656\"%;
s%:26660%:${SIDE_PORT}660%g" $HOME/.side/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.side/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.side/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.side/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.005uside"|g' $HOME/.side/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.side/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.side/config/config.toml

# create service file
sudo tee /etc/systemd/system/sided.service > /dev/null <<EOF
[Unit]
Description=Side node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.side
ExecStart=$(which sided) start --home $HOME/.side
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
sided tendermint unsafe-reset-all --home $HOME/.side
if curl -s --head curl https://testnet-files.itrocket.net/side/snap_side.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/side/snap_side.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side
    else
  echo no have snap
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable sided
sudo systemctl restart sided && sudo journalctl -u sided -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

pruning: custom: 100/0/10 | indexer: null

```bash
source <(curl -s https://itrocket.net/api/testnet/side/autoinstall/)
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
sided keys add $WALLET

# to restore exexuting wallet, use the following command
sided keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(sided keys show $WALLET -a)
VALOPER_ADDRESS=$(sided keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
sided status 2>&1 | jq .SyncInfo

# before creating a validator, you need to fund your wallet and check balance
sided query bank balances $WALLET_ADDRESS
```

### Create validator <a href="#create-validator" id="create-validator"></a>

MonikerIdentityDetailsAmount, usideCommission rateCommission max rateCommission max change rate

```bash
sided tx staking create-validator \
--amount 1000000uside \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(sided tendermint show-validator) \
--moniker "test" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id side-testnet-3 \
--gas auto --fees 1000uside \
-y
```

### Monitoring <a href="#monitoring" id="monitoring"></a>

If you want to have set up a monitoring and alert system use [our cosmos nodes monitoring guide with tenderduty](https://teletype.in/@itrocket/bdJAHvC\_q8h)

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
sudo ufw allow ${SIDE_PORT}656/tcp
sudo ufw enable
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop sided
sudo systemctl disable sided
sudo rm -rf /etc/systemd/system/sided.service
sudo rm $(which sided)
sudo rm -rf $HOME/.side
sed -i "/SIDE_/d" $HOME/.bash_profile
```
