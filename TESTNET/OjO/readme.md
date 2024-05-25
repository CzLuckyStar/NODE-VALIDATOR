# Ojo


Ojo is a decentralized security-first oracle network built to support the Cosmos Ecosystem. Ojo will source price data from a diverse catalog of on and off-chain sources and use advanced security mechanisms to guarantee the integrity of the data it provides.

Chain ID: agamotto

Latest Version Tag: v0.3.1-rc2

Wasm: OFF

Website: https://ojo.network/

Discord: https://discord.gg/fd8Yrex8nC

Twitter: https://twitter.com/ojo_network
`
### Chain explorer
https://explorer.kjnodes.com/ojo-testnet

### Public endpoints
api: https://ojo-testnet.api.kjnodes.com

rpc: https://ojo-testnet.rpc.kjnodes.com

grpc with ssl/tls: ojo-testnet.grpc.kjnodes.com:443
### Peering
state-sync
```
d5519e378247dfb61dfe90652d1fe3e2b3005a5b@ojo-testnet.rpc.kjnodes.com:15056
```
seed-node
```
3f472746f46493309650e5a033076689996c8881@ojo-testnet.rpc.kjnodes.com:15059
```
addrbook
```
curl -Ls https://snapshots.kjnodes.com/ojo-testnet/addrbook.json > $HOME/.ojo/config/addrbook.json
```
live-peers (6)
```
peers="02847a3c4f8f03859e1e3c40c0edee1ecf8a8f24@65.109.69.239:18007,6d24173b781074e73339234ae6feb7f762665685@34.27.139.185:26656,79c008a1bd4d16f9310815246cb8d2de344d054d@35.188.14.155:26656,3d0d6879da6d2b730008a63f0b551b3ee6b1feab@34.133.132.42:26656,e77ff0c664315ad8de5508c4498b9da52383d40e@34.122.6.55:26656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:15056"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.ojo/config/config.toml
```
