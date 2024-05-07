# Snapshot

### Snapshot <a href="#snap" id="snap"></a>

2h ago | size: 352KB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop blockxd

cp $HOME/.blockxd/data/priv_validator_state.json $HOME/.blockxd/priv_validator_state.json.backup

rm -rf $HOME/.blockxd/data $HOME/.blockxd/wasmPath
curl https://mainnet-files.bonynode.online/blockx/snap_blockx.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.blockxd

mv $HOME/.blockxd/priv_validator_state.json.backup $HOME/.blockxd/data/priv_validator_state.json

sudo systemctl restart blockxd && sudo journalctl -u blockxd -f
```
