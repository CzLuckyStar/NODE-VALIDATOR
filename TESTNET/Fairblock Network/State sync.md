# State sync
With our state sync services you will be able to catch up latest chain block in matter of minutes

### Fairblock Network
State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

### Instructions
Stop fairyringd and reset database
```
sudo systemctl stop fairyringd
cp .fairyring/data/priv_validator_state.json .fairyring/priv_validator_state.json.backup
fairyringd tendermint unsafe-reset-all --home .fairyring --keep-addr-book
```
Get and configure the state sync information
```
STATE_SYNC_RPC=https://rpc.cosmos.directory:443/fairyring
BACKUP_RPC=https://fairyring-rpc.lavenderfive.com:443
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
BLOCK_HEIGHT=$(LATEST_HEIGHT | awk '{print $1 - ($1 % 6000)}'); \
TRUST_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
SEEDS="20e1000e88125698264454a884812746c2eb4807@seeds.lavenderfive.com:24756"
```
```
echo -e "Modifying .fairyring/config/config.toml based on the following values:"
echo -e "RPC Address:          $STATE_SYNC_RPC"
echo -e "Latest Block Height:  $LATEST_HEIGHT"
echo -e "Sync Block Height:    $BLOCK_HEIGHT"
echo -e "Trust Hash:           $TRUST_HASH"
```
```
sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$BACKUP_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$TRUST_HASH\"|" \
  -e "s|^seeds *=.*|seeds = \"$SEEDS\"|" \
  .fairyring/config/config.toml
```
```
mv .fairyring/priv_validator_state.json.backup .fairyring/data/priv_validator_state.json
```
### Download latest wasm
`Currently state sync does not support copy of the wasm folder. Therefore, you will have to download it manually.`

```
curl -L https://snapshots.lavenderfive.com/wasm/.fairyring/wasmonly.tar.lz4 | lz4 -dc - | tar -xf - -C .fairyring
```
Restart fairyringd and check the log
```
sudo systemctl restart fairyringd && sudo journalctl -u fairyringd -f --no-hostname -o cat
```
End
