# Upgrade

![](<../../.gitbook/assets/union (1).png>)

| Chain ID        | Latest Version Tag | Custom Port |
| --------------- | ------------------ | ----------- |
| union-testnet-9 | v0.25.0            | 171         |

Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.

### Download and build upgrade binaries <a href="#download-and-build-upgrade-binaries" id="download-and-build-upgrade-binaries"></a>

```
# Download project binaries
mkdir -p $HOME/.union/cosmovisor/upgrades/v0.25.0/bin
wget -O $HOME/.union/cosmovisor/upgrades/v0.25.0/bin/uniond https://snapshots.kjnodes.com/union-testnet/uniond-v0.25.0-linux-amd64
chmod +x $HOME/.union/cosmovisor/upgrades/v0.25.0/bin/uniond

```

_Thats it! Now when upgrade block height is reached, Cosmovisor will handle it automatically!_
