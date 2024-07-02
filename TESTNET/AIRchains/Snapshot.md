# AIRCHAINS SNAPSHOT
## Install tools:
```
sudo apt update && sudo apt install lz4 -y
```

## Stop the service and reset the data:
```
sudo systemctl stop junctiond
cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
rm -rf $HOME/.junction/data
```

## Download latest snapshot
```
curl -o - -L https://snapshots.moonbridge.team/testnet/airchains/snapshot_latest.tar.lz4 | lz4 -dc - | tar -x -C $HOME/.junction
mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json
```

## Start the service and check the logs:
```
sudo systemctl start junctiond && sudo journalctl -u junctiond -f --no-hostname -o cat
```
