
# Allora Snapshot

Snapshots are created automatically every `12 hours` starting from `00:00 UTC`

Pruning: `100/0/10` | Indexer: `null` | Version Tag: `v0.0.10`

## Install tools
```
sudo apt update && sudo apt install lz4 -y
```
## Stop the service and reset the data
```
sudo systemctl stop allorad
cp $HOME/.allorad/data/priv_validator_state.json $HOME/.allorad/priv_validator_state.json.backup
rm -rf $HOME/.allorad/data
```
## Download latest snapshot
```
curl -o - -L https://snapshots.moonbridge.team/tastnet/allora/snapshot_latest.tar.lz4 | lz4 -dc - | tar -x -C $HOME/.allorad
mv $HOME/.allorad/priv_validator_state.json.backup $HOME/.allorad/data/priv_validator_state.json
```
## Start the service and check the logs
```
sudo systemctl start allorad && sudo journalctl -u allorad -f --no-hostname -o cat
```
