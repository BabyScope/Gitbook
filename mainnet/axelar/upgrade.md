---
description: Prepare for and the upcomming chain upgrade using Cosmovisor.
---

# Upgrade

<figure><img src="https://github.com/BabyScope/Gitbook/blob/main/images/axelar.png?raw=true" alt=""><figcaption></figcaption></figure>

**Chain ID**: axelar-dojo-1 | **Latest Version Tag**: v0.35.5 | **Custom Port**: 141

{% hint style="info" %}
Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.
{% endhint %}

## Download and build upgrade binaries

```bash
# Clone project repository
cd $HOME
rm -rf axelar-core
git clone https://github.com/axelarnetwork/axelar-core.git
cd axelar-core
git checkout v0.35.5

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.axelar/cosmovisor/upgrades/v0.35/bin
mv bin/axelard $HOME/.axelar/cosmovisor/upgrades/v0.35/bin/
rm -rf build

```

*Now when upgrade block height is reached, Cosmovisor will handle it automatically!*