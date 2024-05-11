# Upgrade

![](https://services.kjnodes.com/assets/images/logos/lava.png)

| Chain ID       | Latest Version Tag | Custom Port |
| -------------  | ------------------ | ----------- |
| lava-testnet-2 | v2.0.0             | 144         |

Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.

### Download and build upgrade binaries <a href="#download-and-build-upgrade-binaries" id="download-and-build-upgrade-binaries"></a>

```
# Clone project repository
cd $HOME
rm -rf lava
git clone https://github.com/lavanet/lava.git
cd lava
git checkout v2.0.0

# Build binaries
export LAVA_BINARY=lavad
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.lava/cosmovisor/upgrades/v2.0.0/bin
mv build/lavad $HOME/.lava/cosmovisor/upgrades/v2.0.0/bin/
rm -rf build
```

_Thats it! Now when upgrade block height is reached, Cosmovisor will handle it automatically!_
