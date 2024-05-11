---
description: Prepare for and the upcomming chain upgrade using Cosmovisor.
---

# Upgrade

<figure><img src="https://github.com/BabyScope/Gitbook/blob/main/images/evmos.png?raw=true" alt=""><figcaption></figcaption></figure>

**Chain ID**: evmos_9001-2 | **Latest Version Tag**: v18.0.0 | **Custom Port**: 169

{% hint style="info" %}
Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.
{% endhint %}

## Download and build upgrade binaries

```bash
# Clone project repository
cd $HOME
rm -rf evmos
git clone https://github.com/tharsis/evmos.git
cd evmos
git checkout v18.0.0

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.evmosd/cosmovisor/upgrades/v18.0.0/bin
mv build/evmosd $HOME/.evmosd/cosmovisor/upgrades/v18.0.0/bin/
rm -rf build


```

*Now when upgrade block height is reached, Cosmovisor will handle it automatically!*