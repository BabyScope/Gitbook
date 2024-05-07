# Public RPC

### Chain explorer <a href="#chain-explorer" id="chain-explorer"></a>

[Namada.info Explorer](https://namada.info/)

### Public endpoints <a href="#public-endpoints" id="public-endpoints"></a>

Public RPC:[https://namada-testnet-rpc.bonynode.online:443](http://88.99.208.54:11657/)\
Public RPC (node operations):[tcp://namada-testnet-rpc.bonynode.online:12457](tcp://88.99.208.54:11657/)\
Public indexer:[https://namada-testnet-indexer.bonynode.online/block/last](https://namada-testnet-indexer.bonynode.online/block/last)

* encrypted rpc (SDK-compatible): [https://namada-testnet.rpc.bonynode.online](http://88.99.208.54:11657/)

### Peering <a href="#peering" id="peering"></a>

**state-sync**

not supported by chain

**seed-node**

```
sed -i -e 's/^seeds *=.*/seeds = "9f7ae98caa7adcbe2e77f55866c5026ee874293d@namada-testnet.rpc.bonynode.online:12459"/' $HOME/.local/share/namada/shielded-expedition.88f17d1d14/config.toml
```

**addrbook**

```
curl -Ls https://snapshots.bonynode.online/namada-testnet/addrbook.json > $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/config/addrbook.json
```
