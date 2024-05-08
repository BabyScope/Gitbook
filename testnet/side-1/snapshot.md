# Snapshot

RPC:[ https://side-testnet-rpc.bonynode.online](https://side-testnet-rpc.bonynode.online)\
API: [https://side-testnet-api.bonynode.online](https://side-testnet-api.bonynode.online)

### Snapshot <a href="#snap" id="snap"></a>

height: 24544 | 41m ago | size: 2.0GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop sided

cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup

rm -rf $HOME/.side/data $HOME/.side/wasmPath
curl https://testnet-files.itrocket.net/side/snap_side.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side

mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json

sudo systemctl restart sided && sudo journalctl -u sided -f
```

### &#x20;<a href="#snap" id="snap"></a>

#### addrbook: <a href="#addrbook" id="addrbook"></a>

updates every hour

```bash
wget -O $HOME/.side/config/addrbook.json https://testnet-files.itrocket.net/side/addrbook.json
```

#### gRPC: <a href="#grpc" id="grpc"></a>

```bash
side-testnet-grpc.itrocket.net:45090
```

#### peers: <a href="#peer" id="peer"></a>

```bash
bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656
```

#### seeds: <a href="#seed" id="seed"></a>

```bash
9c14080752bdfa33f4624f83cd155e2d3976e303@side-testnet-seed.itrocket.net:45656
```

#### live peers: <a href="#live-peers" id="live-peers"></a>

active peers: 11 (upd. every 10 sec)

```bash
PEERS="bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656,6decdc5565bf5232cdf5597a7784bfe828c32277@158.220.126.137:11656,e9ee4fb923d5aab89207df36ce660ff1b882fc72@136.243.33.177:21656,169332e1a5aad8e49fced765992201774a754cd0@95.216.27.29:34656,2a6d31c23160e49db1f03a884dc7b9602fffe895@176.9.126.85:30004,c8962c5fe2622e969399f98377e775147425cf3f@173.249.44.60:34656,ca3379b48e196c3ef910a08452b459b0f327fdb6@95.216.3.115:34656,2780ffa710b0d42dacc4eeffb4c6bc145ef6636f@38.129.16.236:26656,bae861fd068a26b90235b3677d28d4f37d747e44@173.212.253.77:26656,b588e261519d49e436fc503af5b602810110bd36@194.163.149.7:26656,e52da5e5fecf65abf9d7a3135196240f065deed3@207.180.212.200:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.side/config/config.toml
```

\\

### &#x20;<a href="#snap" id="snap"></a>
