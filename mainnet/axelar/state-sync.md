---
description: >-
  With our state sync services you will be able to catch up latest chain block
  in matter of minutes
---

# State sync

<figure><img src="https://github.com/BabyScope/Gitbook/blob/main/images/axelar.png?raw=true" alt=""><figcaption></figcaption></figure>

State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

### Instructions <a href="#instructions" id="instructions"></a>

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```bash
sudo systemctl stop axelard
cp $HOME/.axelar/data/priv_validator_state.json $HOME/.axelar/priv_validator_state.json.backup
axelard tendermint unsafe-reset-all --keep-addr-book --home $HOME/.axelar
```

### Get and configure the state sync information

```bash
STATE_SYNC_RPC=https://axelar.rpc.kjnodes.com:443
STATE_SYNC_PEER=d9bfa29e0cf9c4ce0cc9c26d98e5d97228f93b0b@axelar.rpc.kjnodes.com:16556
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.axelar/config/config.toml

mv $HOME/.axelar/priv_validator_state.json.backup $HOME/.axelar/data/priv_validator_state.json
```

### Download latest wasm

{% hint style="info" %}
Currently state sync does not support copy of the `wasm` folder. Therefore, you will have to download it manually.
{% endhint %}

```bash
curl -L https://snapshots.kjnodes.com/axelar/wasm_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.axelar
```

### Restart the service and check the log

```bash
sudo systemctl start axelard && sudo journalctl -u axelard -f --no-hostname -o cat
```
