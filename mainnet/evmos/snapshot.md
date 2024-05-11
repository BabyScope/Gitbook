---
description: Catch the latest block faster by using our daily snapshots.
---

# Snapshot

{% hint style="info" %}
Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.
{% endhint %}

## Instructions

### Stop the service and reset the data

```bash
sudo systemctl stop evmosd
cp $HOME/.evmosd/data/priv_validator_state.json $HOME/.evmosd/priv_validator_state.json.backup
rm -rf $HOME/.evmosd/data
```

### Download latest snapshot

```bash
curl -L https://snapshots.stakevillage.net/snapshots/evmos_9001-2/evmos_9001-2_2024-04-04.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.evmosd
cp $HOME/.evmosd/priv_validator_state.json.backup $HOME/.evmosd/data/priv_validator_state.json
```

### Restart the service and check the log

```bash
sudo systemctl start evmos.service && sudo journalctl -u evmos.service -f --no-hostname -o cat
```