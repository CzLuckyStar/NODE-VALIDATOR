
# CLI Cheatsheet

## Key management
### Add new key
```
kopid keys add wallet
```

### Recover existing key

```
kopid keys add wallet --recover
```

### List all keys

```
kopid keys list
```

### Delete key

```
kopid keys delete wallet
```

### Export key to the file

```
kopid keys export wallet
```

### Import key from the file

```
kopid keys import wallet wallet.backup
```

### Wallet balance

```
kopid q bank balances $(kopid keys show wallet -a)
```

## Token management
### Withdraw rewards from all validators

```
kopid tx distribution withdraw-all-rewards --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Withdraw rewards and commissions from your validator

```
kopid tx distribution withdraw-rewards $(kopid keys show wallet --bech val -a) --commission --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Delegate tokens to yourself

```
kopid tx staking delegate $(kopid keys show wallet --bech val -a) 1000000ukopi --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Delegate tokens to validator

```
kopid tx staking delegate <TO_VALOPER_ADDRESS> 1000000ukopi --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Redelegate tokens to another validator

```
kopid tx staking redelegate $(kopid keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000ukopi --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Unbond tokens from your validator

```
kopid tx staking unbond $(kopid keys show wallet --bech val -a) 1000000ukopi --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Send tokens to the wallet

```
kopid tx bank send wallet <TO_WALLET_ADDRESS> 1000000ukopi --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

## Validator management
### Validator info

```
kopid status 2>&1 | jq .validator_info
```

### Validator details

```
kopid q staking validator $(kopid keys show wallet --bech val -a)
```

### Check if validator key is correct

```
[[ $(kopid q staking validator $(kopid keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(kopid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### List all active validators

```
kopid q staking validators -oj --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### List all inactive validators

```
kopid q staking validators -oj --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### Edit existing validator

```
kopid tx staking edit-validator \
  --new-moniker "YOUR_MONIKER_NAME" \
  --identity "YOUR_KEYBASE_ID" \
  --details "YOUR_DETAILS" \
  --website "YOUR_WEBSITE_URL" \
  --security-contact "YOUR_EMAIL_ADDRESS" \
  --chain-id kopi-test-5 \
  --commission-rate 0.05 \
  --from wallet \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0ukopi \
  -y
```

### Jail reason

```
kopid query slashing signing-info $(kopid tendermint show-validator)
```

### Unjail validator

```
kopid tx slashing unjail --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

## Governance
### Create a new offer

```
kopid tx gov submit-proposal \
  --title "" \
  --description "" \
  --deposit 10000000ukopi \
  --type Text
  --from wallet \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0ukopi \
  -y
```

### List all proposals

```
kopid query gov proposals
```

### View proposal by ID

```
kopid query gov proposal 1
```

### Vote “YES”

```
kopid tx gov vote 1 yes --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Vote “NO”

```
kopid tx gov vote 1 no --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Vote “ABSTAIN”

```
kopid tx gov vote 1 abstain --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

### Vote “NOWITHVETO”

```
kopid tx gov vote 1 NoWithVeto --from wallet --chain-id kopi-test-5 --gas-adjustment 1.4 --gas auto --gas-prices 0ukopi -y
```

## Maintenance
### Get sync info

```
kopid status 2>&1 | jq .sync_info
```

### Get node peer

```
echo $(kopid tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.kopid/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Get live peers

```
curl -sS http://localhost:13657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

### Enable Prometheus

```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.kopid/config/config.toml
```

### Set minimum gas price

```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0ukopi\"|" $HOME/.kopid/config/app.toml
```

### Disable indexer

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.kopid/config/config.toml
```

### Enable indexer

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.kopid/config/config.toml
```

### Update pruning

```
sed -i -e 's|^pruning *=.*|pruning = "custom"|' $HOME/.kopid/config/app.toml
sed -i -e 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|' $HOME/.kopid/config/app.toml
sed -i -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' $HOME/.kopid/config/app.toml
sed -i -e 's|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.kopid/config/app.toml
```

### Filter peers and max peers

```
sed -i -e 's|^filter_peers *=.*|filter_peers = "true"|' $HOME/.kopid/config/config.toml
sed -i -e 's|^max_num_inbound_peers *=.*|max_num_inbound_peers = "50"|' $HOME/.kopid/config/config.toml
sed -i -e 's|^max_num_outbound_peers *=.*|max_num_outbound_peers = "20"|' $HOME/.kopid/config/config.toml
```

### Update ports

```
CUSTOM_PORT=136
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.kopid/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.kopid/config/app.toml
```

### Reset chain data

```
kopid tendermint unsafe-reset-all --keep-addr-book --home $HOME/.kopid
```

### Delete node

```
sudo systemctl stop kopid
sudo systemctl disable kopid
sudo rm -rf /etc/systemd/system/kopid.service
sudo systemctl daemon-reload
sudo rm -f $(which kopid)
sudo rm -rf $HOME/.kopid
sudo rm -rf $HOME/kopi
```

## Service Management
### Status service

```
sudo systemctl status kopid
```

### Start service

```
sudo systemctl start kopid
```

### Stop service

```
sudo systemctl stop kopid
```

### Restart service

```
sudo systemctl restart kopid
```

### Logs service

```
sudo journalctl -u kopid -f --no-hostname -o cat
```

### Reload service

```
sudo systemctl daemon-reload
```

### Enable service

```
sudo systemctl enable kopid
```

### Disable service

```
sudo systemctl disable kopid
```

# END
