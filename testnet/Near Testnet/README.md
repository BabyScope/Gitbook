# 🚀 NAMADA

![](https://services.kjnodes.com/assets/images/logos/namada.png)

Namada is a privacy-for-assets centric blockchain using zero-knowledge proof technology. Namada’s cryptographical features give users asset-agnostic, interchain privacy, and is being built by the Anoma foundation.

| Chain ID                                                                                    | Latest Version Tag                                              | Wasm |
| ------------------------------------------------------------------------------------------- | --------------------------------------------------------------- | ---- |
| [shielded-expedition.88f17d1d14](https://docs.namada.net/networks/testnets/testnet-history) | [v0.31.6](https://github.com/anoma/namada/releases/tag/v0.31.6) | ON   |

[Website](https://namada.net/) | [Discord](https://discord.com/invite/namada) | [Twitter](https://twitter.com/namada)

### Chain explorer <a href="#chain-explorer" id="chain-explorer"></a>

[Namada.info Explorer](https://namada.info/)

### Public endpoints <a href="#public-endpoints" id="public-endpoints"></a>

* rpc node for CLI: [tcp://namada-testnet.rpc.bonynode.online:80](tcp://namada-testnet.rpc.bonynode.online:80)
* encrypted rpc (SDK-compatible): [https://namada-testnet.rpc.bonynode.online](https://namada-testnet.rpc.bonynode.online)

### Peering <a href="#peering" id="peering"></a>

**state-sync**

not supported by chain

**seed-node**

```
sed -i -e 's/^seeds *=.*/seeds = "3f472746f46493309650e5a033076689996c8881@namada-testnet.rpc.bonynode.online:26659"/' $HOME/.local/share/namada/shielded-expedition.88f17d1d14/config.toml
```

**addrbook**

```
curl -Ls https://snapshots.bonynode.online/namada-testnet/addrbook.json > $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/config/addrbook.json
```
