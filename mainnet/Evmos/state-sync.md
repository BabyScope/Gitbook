# State sync

### State Sync <a href="#sync" id="sync"></a>

If you don't want to wait for a long synchronization you can use:

```bash
sudo systemctl stop blockxd

cp $HOME/.blockxd/data/priv_validator_state.json $HOME/.blockxd/priv_validator_state.json.backup
blockxd tendermint unsafe-reset-all --home $HOME/.blockxd

peers="7e1c5fc00878f2a632e7c1fca3a279dcbd2e3f3c@blockx-mainnet-peer.itrocket.net:12656,cf9c69c4d817a26e7fd4700a3d57c893b551c7fa@65.109.33.26:16656,d2c3dbb7bcf808253f8d8b42fed4a90f7e4e0004@[2a01:4f9:3081:4e45::3]:26656,58c9d617e964f558c18fd2347ad606b182febfe1@135.181.212.139:16656,203082daff197dca66797d955d103740875d84d8@[2a01:4f9:3051:19c2::2]:12856,72639ce4ce7e0260d7ae129e6acc07dcb54d6af1@167.235.102.45:20656,4580170247ae7918c3c5a087632278f8004dee96@158.220.117.55:26656,34d08633547fc406095ff6d730fdfe65d34b96d0@158.69.125.73:11356,479dfa1948f49b08810cd16bf6c2d3256ae85423@137.184.7.64:26656,85d0069266e78896f9d9e17915cdfd271ba91dfd@146.190.153.165:26656,e15f4d31281036c69fa17269d9b26ff8733511c6@147.182.238.235:26656,9b84b33d44a880a520006ae9f75ef030b259cbaf@137.184.38.212:26656,bbe679ddc774dc91b962985c7339a2e7934b8451@207.180.250.5:26656,bc152258668e673a3b63f964fa75afdd478078c7@89.58.13.159:39656"  
SNAP_RPC="https://blockx-mainnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.blockxd/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.blockxd/config/config.toml

mv $HOME/.blockxd/priv_validator_state.json.backup $HOME/.blockxd/data/priv_validator_state.json

sudo systemctl restart blockxd && sudo journalctl -u blockxd -f
```
