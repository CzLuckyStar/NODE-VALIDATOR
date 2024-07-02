
# CLI Cheatsheet

## Key management

### Add new key
```
allorad keys add wallet
```
### Recover existing key
```
allorad keys add wallet --recover
```
### List all keys
```
allorad keys list
```
### Delete key
```
allorad keys delete wallet
```
### Export key to the file
```
allorad keys export wallet
```
### Import key from the file
```
allorad keys import wallet wallet.backup
```
### Wallet balance
```
allorad q bank balances $(allorad keys show wallet -a)
```
## Token management
### Withdraw rewards from all validators
```
allorad tx distribution withdraw-all-rewards --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Withdraw rewards and commissions from your validator
```
allorad tx distribution withdraw-rewards $(allorad keys show wallet --bech val -a) --commission --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Delegate tokens to yourself
```
allorad tx staking delegate $(allorad keys show wallet --bech val -a) 1000000uallo --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Delegate tokens to validator
```
allorad tx staking delegate <TO_VALOPER_ADDRESS> 1000000uallo --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Redelegate tokens to another validator
```
allorad tx staking redelegate $(allorad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uallo --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Unbond tokens from your validator
```
allorad tx staking unbond $(allorad keys show wallet --bech val -a) 1000000uallo --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Send tokens to the wallet
```
allorad tx bank send wallet <TO_WALLET_ADDRESS> 1000000uallo --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
## Validator management
### Validator info
```
allorad status 2>&1 | jq .ValidatorInfo
```
### Validator details
```
allorad q staking validator $(allorad keys show wallet --bech val -a)
```
### Check if validator key is correct
```
[[ $(allorad q staking validator $(allorad keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(allorad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```
### List all active validators
```
allorad q staking validators -oj --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
### List all inactive validators
```
allorad q staking validators -oj --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
### Edit existing validator
```
allorad tx staking edit-validator \
  --new-moniker "YOUR_MONIKER_NAME" \
  --identity "YOUR_KEYBASE_ID" \
  --details "YOUR_DETAILS" \
  --website "YOUR_WEBSITE_URL" \
  --security-contact "YOUR_EMAIL_ADDRESS" \
  --chain-id testnet \
  --commission-rate 0.05 \
  --from wallet \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0uallo \
  -y
```
### Jail reason
```
allorad query slashing signing-info $(allorad tendermint show-validator)
```
### Unjail validator
```
allorad tx slashing unjail --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
## Governance
### Create a new offer
```
allorad tx gov submit-proposal \
  --title "" \
  --description "" \
  --deposit 10000000uallo \
  --type Text
  --from wallet \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0uallo \
  -y
```
### List all proposals
```
allorad query gov proposals
```
### View proposal by ID
```
allorad query gov proposal 1
```
### Vote `“YES”`

```
allorad tx gov vote 1 yes --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Vote `“NO”`
```
allorad tx gov vote 1 no --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Vote `“ABSTAIN”`
```
allorad tx gov vote 1 abstain --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
### Vote `“NOWITHVETO”`
```
allorad tx gov vote 1 NoWithVeto --from wallet --chain-id testnet --gas-adjustment 1.4 --gas auto --gas-prices 0uallo -y
```
## Maintenance
### Get sync info
```
allorad status 2>&1 | jq .SyncInfo
```
### Get node peer
```
echo $(allorad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.allorad/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```
### Get live peers
```
curl -sS http://localhost:12557/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```
### Enable Prometheus
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.allorad/config/config.toml
```
### Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0uallo\"|" $HOME/.allorad/config/app.toml
```
### Disable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.allorad/config/config.toml
```
### Enable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.allorad/config/config.toml
```
### Update pruning
```
sed -i -e 's|^pruning *=.*|pruning = "custom"|' $HOME/.allorad/config/app.toml
sed -i -e 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|' $HOME/.allorad/config/app.toml
sed -i -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' $HOME/.allorad/config/app.toml
sed -i -e 's|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.allorad/config/app.toml
```
### Filter peers and max peers
```
sed -i -e 's|^filter_peers *=.*|filter_peers = "true"|' $HOME/.allorad/config/config.toml
sed -i -e 's|^max_num_inbound_peers *=.*|max_num_inbound_peers = "50"|' $HOME/.allorad/config/config.toml
sed -i -e 's|^max_num_outbound_peers *=.*|max_num_outbound_peers = "20"|' $HOME/.allorad/config/config.toml
```
### Update ports
```
CUSTOM_PORT=125
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.allorad/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.allorad/config/app.toml
```
### Reset chain data
```
allorad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.allorad
```
### Delete node
```
sudo systemctl stop allorad
sudo systemctl disable allorad
sudo rm -rf /etc/systemd/system/allorad.service
sudo systemctl daemon-reload
sudo rm -f $(which allorad)
sudo rm -rf $HOME/.allorad
sudo rm -rf $HOME/allora-chain
```
## Service Management
### Status service
```
sudo systemctl status allorad
```
### Start service
```
sudo systemctl start allorad
```
### Stop service
```
sudo systemctl stop allorad
```
### Restart service
```
sudo systemctl restart allorad
```
### Logs service
```
sudo journalctl -u allorad -f --no-hostname -o cat
```
### Reload service
```
sudo systemctl daemon-reload
```
### Enable service
```
sudo systemctl enable allorad
```
### Disable service
```
sudo systemctl disable allorad
```


# END
