# Upgrade

![](https://services.kjnodes.com/assets/images/logos/elys.png)

| Chain ID      | Latest Version Tag | Custom Port |
| ------------- | ------------------ | ----------- |
| elystestnet-1 | v0.30.0            | 153         |

Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.

### Download and build upgrade binaries <a href="#download-and-build-upgrade-binaries" id="download-and-build-upgrade-binaries"></a>

```
# Clone project repository
cd $HOME
rm -rf elys
git clone https://github.com/elys-network/elys.git
cd elys
git checkout v0.30.0

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.elys/cosmovisor/upgrades/v0.30.0/bin
mv build/elysd $HOME/.elys/cosmovisor/upgrades/v0.30.0/bin/
rm -rf build
```

_Thats it! Now when upgrade block height is reached, Cosmovisor will handle it automatically!_
