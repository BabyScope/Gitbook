---
description: Catch the latest block faster by using our daily snapshots.
---

# Snapshot

{% hint style="info" %}
Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.
{% endhint %}

Snapshots are taken automatically every 6 hours starting at **07:00 UTC**

**pruning**: 100/0/19 | **indexer**: null | **version tag**: v0.35

## Instructions

### Stop the service and reset the data

```bash
sudo systemctl stop axelard
cp $HOME/.axelar/data/priv_validator_state.json $HOME/.axelar/priv_validator_state.json.backup
rm -rf $HOME/.axelar/data
```

### Download latest snapshot

```bash
curl -L https://snapshots.kjnodes.com/axelar/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.axelar
mv $HOME/.axelar/priv_validator_state.json.backup $HOME/.axelar/data/priv_validator_state.json
```

### Restart the service and check the log

```bash
sudo systemctl start axelard && sudo journalctl -u axelard -f --no-hostname -o cat
```