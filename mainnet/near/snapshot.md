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
sudo systemctl stop neard
rm -rf ~/.neard/data
```

### Download latest snapshot

```bash
aws s3 --no-sign-request cp s3://near-protocol-public/backups/mainnet/rpc/latest .
LATEST=$(cat latest)
aws s3 --no-sign-request cp --no-sign-request --recursive s3://near-protocol-public/backups/mainnet/rpc/$LATEST ~/.near/data
```

### Restart the service and check the log

```bash
sudo systemctl restart neard && sudo journalctl -u neard -f --no-hostname -o cat
```