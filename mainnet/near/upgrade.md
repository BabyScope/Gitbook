---
description: Prepare for and the upcomming chain upgrade using Cosmovisor.
---

# Upgrade

<figure><img src="../../images/near.png" alt=""><figcaption></figcaption></figure>

**Chain ID**: mainnet | **Latest Version Tag**: v1.39.1

## Download and build upgrade binaries

```bash
# Clone project repository
cd $HOME
rm -rf nearcore
git clone https://github.com/near/nearcore
cd nearcore
git fetch origin --tags
git checkout tags/1.39.2 -b mynode
make release
sudo mv $HOME/nearcore/target/release/neard /usr/local/bin/neard
sudo systemctl restart neard && sudo journalctl -u neard -f --no-hostname -o cat
```
