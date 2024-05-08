# ELYS

![](https://services.kjnodes.com/assets/images/logos/elys.png)

Elys is a fast layer 1 blockchain powered by the Cosmos-SDK. It is the first, and only, decentralized suite of financial applications in the Cosmos ecosystem that has built-in native bridging capabilities for optimal user experience and value capture, and native margin trading capabilities that are powered by liquidity pools & liquidity providers.

| Chain ID      | Latest Version Tag | Wasm |
| ------------- | ------------------ | ---- |
| elystestnet-1 | v0.29.24           | ON   |

[Website](https://elys.network/) | [Discord](https://discord.gg/R9Gr6Vh7vC) | [Twitter](https://twitter.com/elys\_network)

Subscribe to our free [🤖 Testnet Proposal Bot](https://t.me/kjnodes\_testnet\_proposal\_bot) to never miss upcoming proposals

### Chain explorer <a href="#chain-explorer" id="chain-explorer"></a>

[https://explorer.kjnodes.com/elys-testnet](https://explorer.kjnodes.com/elys-testnet)

### Public endpoints <a href="#public-endpoints" id="public-endpoints"></a>

* api: [https://elys-testnet.api.kjnodes.com](https://elys-testnet.api.kjnodes.com/)
* rpc: [https://elys-testnet.rpc.kjnodes.com](https://elys-testnet.rpc.kjnodes.com/)
* grpc with ssl/tls: elys-testnet.grpc.kjnodes.com:443

### Peering <a href="#peering" id="peering"></a>

**state-sync**

```
d5519e378247dfb61dfe90652d1fe3e2b3005a5b@elys-testnet.rpc.kjnodes.com:15356
```

**seed-node**

```
3f472746f46493309650e5a033076689996c8881@elys-testnet.rpc.kjnodes.com:15359
```

**addrbook**

```
curl -Ls https://snapshots.kjnodes.com/elys-testnet/addrbook.json > $HOME/.elys/config/addrbook.json
```

**live-peers** (29)

```
peers="89304292aceb62aa0f2b9970a921740f114e6d60@65.109.93.35:57656,43156c426ebd688489125fb2df4b088649360d27@78.46.103.246:26656,2cd829367434b1082971baa017f5f7ef9db7b5f2@51.79.229.175:38656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:15356,a346d8325a9c3cd40e32236eb6de031d1a2d895e@95.217.107.96:26156,4d056b4c51d331078b258195a199bba8f6299483@185.169.252.221:26656,cdf9ae8529aa00e6e6703b28f3dcfdd37e07b27c@147.135.9.107:26656,587e0c84a487b2e0782e5d9b80ded838db9512b9@78.110.161.68:26656,463805d557e309c599e25a1284c421919decec42@5.161.206.6:22056,734a87b41a015faf59a7d6266deea190421476c2@167.235.13.19:27656,61284a4d71cd3a33771640b42f40b2afda389a1e@78.110.161.69:26656,036a291fc74d7a8fcc9bbea5927a88a30d173f36@136.243.104.103:21256,501767323c5223bfe138d916189cb5427f7e3931@104.193.254.42:27656,ae22b82b1dc34fa0b1a64854168692310f562136@147.135.104.10:26656,5572d8466cf792749f8daa1ace3722c196f76816@185.218.125.187:26656,6ff344af4ced5a182dda3b58514969d15c6a58b1@65.108.206.74:16656,a6085096ab5c44dacdea4c0ca83f194f29a5485a@88.198.39.169:34656,f3230e2103911d7712d4a43e3d21b00e1ff264fb@37.252.186.197:26656,40ec65e34f5800854c577bc9386ce82ed3fb4740@144.76.97.251:44656,bbf8ef70a32c3248a30ab10b2bff399e73c6e03c@65.21.198.100:21256,40ba15fb8f15354bf563486f3007b59d1a19b03b@169.0.116.106:26656,4425ff58bec9fa581ba49ba4ea89608c4e8dc825@95.217.89.202:11232,86987eeff225699e67a6543de3622b8a986cce28@91.183.62.162:26656,8c23c1f060b69d9856d61d921038593deb5a969d@95.234.151.124:26656,039eaf676ba25e3e27190a34f4993825c3362ce4@167.114.118.234:26706,63ba04db4669c263072e54c8b5a577d538d6574c@208.64.58.207:26656,0845072d96aaed251dc9a06ccb1ed6f173625ba8@154.53.57.227:26656,b65d112663ba452b3c283db45e627aa2c4e2c8cf@178.162.166.24:47656,78aa6b222ae1f619bef03a9d98cb958dfcccc3a8@46.4.5.45:22056"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.elys/config/config.toml
```
