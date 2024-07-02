# StateSync
## Stop the service and reset the data
```
sudo systemctl stop allorad
cp $HOME/.allorad/data/priv_validator_state.json $HOME/.allorad/priv_validator_state.json.backup
allorad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.allorad
```
## Get and configure the state sync information
```
STATE_SYNC_RPC=https://allora-test.rpc.moonbridge.team:443
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  $HOME/.allorad/config/config.toml

mv $HOME/.allorad/priv_validator_state.json.backup $HOME/.allorad/data/priv_validator_state.json
```
## Start the service and check the log
```
sudo systemctl start allorad && sudo journalctl -u allorad -f --no-hostname -o cat
```
## Disable synchronization with StateSync
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.allorad/config/config.toml
sudo systemctl restart allorad && sudo journalctl -u allorad -f --no-hostname -o cat
```
