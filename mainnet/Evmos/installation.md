# Installation

### Manual Installation <a href="#installation" id="installation"></a>

Official DocumentationRecommended Hardware: 4 Cores, 8GB RAM, 200GB of storage (NVME)

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

Node NameWalletPort

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
echo "export BLOCKX_CHAIN_ID="blockx_100-1"" >> $HOME/.bash_profile
echo "export BLOCKX_PORT="49"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
rm -rf networks
git clone https://github.com/BlockXLabs/networks
cd ~/networks/chains/blockx_100-1/source
make install

# config and init app
blockxd config node tcp://localhost:${BLOCKX_PORT}657
blockxd config keyring-backend os
blockxd config chain-id blockx_100-1
blockxd init "test" --chain-id blockx_100-1

# download genesis and addrbook
wget -O $HOME/.blockxd/config/genesis.json https://mainnet-files.itrocket.net/blockx/genesis.json
wget -O $HOME/.blockxd/config/addrbook.json https://mainnet-files.itrocket.net/blockx/addrbook.json

# set seeds and peers
SEEDS="4452d0be36c123b971c2b052c54b2645fd3122a9@blockx-mainnet-seed.itrocket.net:19656"
PEERS="7e1c5fc00878f2a632e7c1fca3a279dcbd2e3f3c@blockx-mainnet-peer.itrocket.net:12656,cf9c69c4d817a26e7fd4700a3d57c893b551c7fa@65.109.33.26:16656,d2c3dbb7bcf808253f8d8b42fed4a90f7e4e0004@[2a01:4f9:3081:4e45::3]:26656,58c9d617e964f558c18fd2347ad606b182febfe1@135.181.212.139:16656,203082daff197dca66797d955d103740875d84d8@[2a01:4f9:3051:19c2::2]:12856,72639ce4ce7e0260d7ae129e6acc07dcb54d6af1@167.235.102.45:20656,4580170247ae7918c3c5a087632278f8004dee96@158.220.117.55:26656,34d08633547fc406095ff6d730fdfe65d34b96d0@158.69.125.73:11356,479dfa1948f49b08810cd16bf6c2d3256ae85423@137.184.7.64:26656,85d0069266e78896f9d9e17915cdfd271ba91dfd@146.190.153.165:26656,e15f4d31281036c69fa17269d9b26ff8733511c6@147.182.238.235:26656,9b84b33d44a880a520006ae9f75ef030b259cbaf@137.184.38.212:26656,bbe679ddc774dc91b962985c7339a2e7934b8451@207.180.250.5:26656,bc152258668e673a3b63f964fa75afdd478078c7@89.58.13.159:39656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.blockxd/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${BLOCKX_PORT}317%g;
s%:8080%:${BLOCKX_PORT}080%g;
s%:9090%:${BLOCKX_PORT}090%g;
s%:9091%:${BLOCKX_PORT}091%g;
s%:8545%:${BLOCKX_PORT}545%g;
s%:8546%:${BLOCKX_PORT}546%g;
s%:6065%:${BLOCKX_PORT}065%g" $HOME/.blockxd/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${BLOCKX_PORT}658%g;
s%:26657%:${BLOCKX_PORT}657%g;
s%:6060%:${BLOCKX_PORT}060%g;
s%:26656%:${BLOCKX_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${BLOCKX_PORT}656\"%;
s%:26660%:${BLOCKX_PORT}660%g" $HOME/.blockxd/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.blockxd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.blockxd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.blockxd/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0abcx"|g' $HOME/.blockxd/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.blockxd/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.blockxd/config/config.toml

# create service file
sudo tee /etc/systemd/system/blockxd.service > /dev/null <<EOF
[Unit]
Description=Blockx node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.blockxd
ExecStart=$(which blockxd) start --home $HOME/.blockxd
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
blockxd tendermint unsafe-reset-all --home $HOME/.blockxd
if curl -s --head curl https://mainnet-files.itrocket.net/blockx/snap_blockx.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://mainnet-files.itrocket.net/blockx/snap_blockx.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.blockxd
    else
  echo no have snap
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable blockxd
sudo systemctl restart blockxd && sudo journalctl -u blockxd -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

pruning: custom: 100/0/10 | indexer: null

```bash
source <(curl -s https://itrocket.net/api/mainnet/blockx/autoinstall/)
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
blockxd keys add $WALLET

# to restore exexuting wallet, use the following command
blockxd keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(blockxd keys show $WALLET -a)
VALOPER_ADDRESS=$(blockxd keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
blockxd status 2>&1 | jq .SyncInfo

# before creating a validator, you need to fund your wallet and check balance
blockxd query bank balances $WALLET_ADDRESS
```

### Create validator <a href="#create-validator" id="create-validator"></a>

MonikerIdentityDetailsAmount, abcxCommission rateCommission max rateCommission max change rate

```bash
blockxd tx staking create-validator \
--amount 1000000abcx \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(blockxd tendermint show-validator) \
--moniker "test" \
--identity "" \
--details "I love blockchain 💚" \
--chain-id blockx_100-1 \
--gas=300000 --gas-prices=1000000000abcx \
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
sudo ufw allow ${BLOCKX_PORT}656/tcp
sudo ufw enable
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop blockxd
sudo systemctl disable blockxd
sudo rm -rf /etc/systemd/system/blockxd.service
sudo rm $(which blockxd)
sudo rm -rf $HOME/.blockxd
sed -i "/BLOCKX_/d" $HOME/.bash_profile
```
