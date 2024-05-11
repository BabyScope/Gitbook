# Snapshot

### Snapshot <a href="#snap" id="snap"></a>

height: 854136 | 3h ago | size: 1.1GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop lava.service
cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup
rm -rf $HOME/.lava/data

curl -L https://snapshots.kjnodes.com/lava-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.lava
mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json


sudo systemctl start lava.service && sudo journalctl -u lava.service -f --no-hostname -o cat

```

