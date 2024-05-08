# State sync

### State Sync <a href="#sync" id="sync"></a>

If you don't want to wait for a long synchronization you can use:

```bash
sudo systemctl stop sided

cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
sided tendermint unsafe-reset-all --home $HOME/.side

peers="bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656,6decdc5565bf5232cdf5597a7784bfe828c32277@158.220.126.137:11656,e9ee4fb923d5aab89207df36ce660ff1b882fc72@136.243.33.177:21656,169332e1a5aad8e49fced765992201774a754cd0@95.216.27.29:34656,2a6d31c23160e49db1f03a884dc7b9602fffe895@176.9.126.85:30004,c8962c5fe2622e969399f98377e775147425cf3f@173.249.44.60:34656,ca3379b48e196c3ef910a08452b459b0f327fdb6@95.216.3.115:34656,2780ffa710b0d42dacc4eeffb4c6bc145ef6636f@38.129.16.236:26656,bae861fd068a26b90235b3677d28d4f37d747e44@173.212.253.77:26656,b588e261519d49e436fc503af5b602810110bd36@194.163.149.7:26656,e52da5e5fecf65abf9d7a3135196240f065deed3@207.180.212.200:26656"  
SNAP_RPC="https://side-testnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.side/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.side/config/config.toml

mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json

sudo systemctl restart sided && sudo journalctl -u sided -f
```

### Wasm <a href="#wasm" id="wasm"></a>

updates every hour

```bash
curl https://testnet-files.itrocket.net/side/wasmPath_side.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side 
sudo systemctl restart sided && sudo journalctl -u sided -f
```
