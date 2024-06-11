# Provider

## Installation Provider

![](https://services.kjnodes.com/assets/images/logos/lava.png)

| Proces | Latest Version Tag |
| ------ | ------------------ |
| Lavap  | v2.1.2             |

**Create config file**

```
RPC=$(cat $HOME/.lava/config/config.toml | sed -n '/TCP or UNIX socket address for the RPC server to listen on/{n;p;}' | sed 's/.*://; s/".*//')
GRPC=$(cat $HOME/.lava/config/app.toml | sed -n '/Address defines the gRPC server address to bind to/{n;p;}' | sed 's/.*://; s/".*//')
API=$(cat $HOME/.lava/config/app.toml | sed -n '/Address defines the API server to listen on./{n;p;}' | sed 's/.*://; s/".*//')

echo "RPC:"$RPC "GRPC:"$GRPC "API:"$API

mkdir $HOME/config
sudo tee << EOF >/dev/null $HOME/config/lavaprovider.yml
endpoints:
  - api-interface: tendermintrpc
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:29667
      disable-tls: true
    node-urls:
      - url: ws://127.0.0.1:$RPC/websocket
      - url: http://127.0.0.1:$RPC
  - api-interface: grpc
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:29667
      disable-tls: true
    node-urls:
      url: 127.0.0.1:$GRPC
  - api-interface: rest
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:29667
      disable-tls: true
    node-urls:
      url: http://127.0.0.1:$API
EOF
```

**Download and build binaries**

```
# Clone project repository
cd $HOME
rm -rf lava
git clone https://github.com/lavanet/lava.git
cd lava
git checkout v2.1.2

make install-all
```

**Create a service**

```
tee /etc/systemd/system/lavap.service > /dev/null << EOF
[Unit]
Description=Lava provider
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/config
ExecStart=$(which lavap) rpcprovider lavaprovider.yml --geolocation 2 --from wallet --chain-id lava-testnet-2 --keyring-backend test
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
systemctl enable lavap
```

**Start service and check the logs**

```
sudo systemctl start lavap && sudo journalctl -u lavap -f --no-hostname -o cat
```

## Useful commands

**Stake new Provider**

```
MONIKER="BabyScope"
DOMEN=provider.babyscope.nl
PORT=443
echo $MONIKER $DOMEN $PORT
lavap tx pairing stake-provider LAV1 "50000000000ulava" "$DOMEN:$PORT,2" 2 lava@valoper18rtt3ka0jc85qvvcnct0t7ayq6fva7697l737q --from wallet --provider-moniker "$MONIKER"  --delegate-limit "0ulava" --gas-prices 0.1ulava --gas-adjustment 1.5 --gas auto -y
```

**Modify**

```
lavap tx pairing modify-provider LAV1 --amount "50000000000ulava" --endpoints "my-provider-africa.com:443,AF my-provider-europe.com:443,EU" --geolocation "AF,EU" --from wallet --gas-prices 0.1ulava --gas-adjustment 1.5 --gas auto -y
```

**Unstake**

```
lavap tx pairing unstake-provider LAV1 --from wallet --gas-prices 0.1ulava --gas-adjustment 1.5 --gas auto -y
```

**Test**

```
lavap test rpcprovider --from wallet
```

**Unjail end time**

```
lavap q pairing provider   <provider address> --node tcp://localhost:20002 | grep "end" 
```

**Account info**

```
lavap query pairing account-info $(lavad keys show wallet -a) -oe | jq
```

**Upgrade**

```
cd $HOME
rm -rf lava
git clone https://github.com/lavanet/lava.git
cd lava
git checkout v2.1.2

export LAVA_BINARY=lavap
make install

sudo systemctl restart lavap && sudo journalctl -u lavap -f --no-hostname -o cat
```
