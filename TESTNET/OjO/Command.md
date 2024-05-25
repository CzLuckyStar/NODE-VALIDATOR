# COMMAND
# 🔑 Key management
### ADD NEW KEY
```
ojod keys add wallet
```
### RECOVER EXISTING KEY
```
ojod keys add wallet --recover
```
### LIST ALL KEYS
```
ojod keys list
```
### DELETE KEY
```
ojod keys delete wallet
```
### EXPORT KEY TO A FILE
```
ojod keys export wallet
```
### IMPORT KEY FROM A FILE
```
ojod keys import wallet wallet.backup
```
### QUERY WALLET BALANCE
```
ojod q bank balances $(ojod keys show wallet -a)
```
# 👷 Validator management
Please make sure you have adjusted moniker, identity, details and website to match your values.

### CREATE NEW VALIDATOR
```
ojod tx staking create-validator \
--amount 1000000uojo \
--pubkey $(ojod tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id agamotto \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0uojo \
-y
```
### EDIT EXISTING VALIDATOR
```
ojod tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id agamotto \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0uojo \
-y
```
### UNJAIL VALIDATOR
```
ojod tx slashing unjail --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```

### JAIL REASON
```
ojod query slashing signing-info $(ojod tendermint show-validator)
```
### LIST ALL ACTIVE VALIDATORS
```
ojod q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
### LIST ALL INACTIVE VALIDATORS
```
ojod q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
### VIEW VALIDATOR DETAILS
```
ojod q staking validator $(ojod keys show wallet --bech val -a)
```
# 💲 Token management
### WITHDRAW REWARDS FROM ALL VALIDATORS
```
ojod tx distribution withdraw-all-rewards --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR
```
ojod tx distribution withdraw-rewards $(ojod keys show wallet --bech val -a) --commission --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### DELEGATE TOKENS TO YOURSELF
```
ojod tx staking delegate $(ojod keys show wallet --bech val -a) 1000000uojo --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### DELEGATE TOKENS TO VALIDATOR
```
ojod tx staking delegate <TO_VALOPER_ADDRESS> 1000000uojo --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### REDELEGATE TOKENS TO ANOTHER VALIDATOR
```
ojod tx staking redelegate $(ojod keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uojo --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### UNBOND TOKENS FROM YOUR VALIDATOR
```
ojod tx staking unbond $(ojod keys show wallet --bech val -a) 1000000uojo --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### SEND TOKENS TO THE WALLET
```
ojod tx bank send wallet <TO_WALLET_ADDRESS> 1000000uojo --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
# 🗳 Governance
### LIST ALL PROPOSALS
```
ojod query gov proposals
```
### VIEW PROPOSAL BY ID
```
ojod query gov proposal 1
```
### VOTE ‘YES’
```
ojod tx gov vote 1 yes --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### VOTE ‘NO’
```
ojod tx gov vote 1 no --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### VOTE ‘ABSTAIN’
```
ojod tx gov vote 1 abstain --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
### VOTE ‘NOWITHVETO’
```
ojod tx gov vote 1 NoWithVeto --from wallet --chain-id agamotto --gas-adjustment 1.4 --gas auto --gas-prices 0uojo -y
```
# ⚡️ Utility
### UPDATE PORTS
```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.ojo/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.ojo/config/app.toml
```
### UPDATE INDEXER
Disable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.ojo/config/config.toml
```
Enable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.ojo/config/config.toml
```
UPDATE PRUNING
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.ojo/config/app.toml
```
# 🚨 Maintenance
### GET VALIDATOR INFO
```
ojod status 2>&1 | jq .ValidatorInfo
```
### GET SYNC INFO
```
ojod status 2>&1 | jq .SyncInfo
```
### GET NODE PEER
```
echo $(ojod tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.ojo/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```
### CHECK IF VALIDATOR KEY IS CORRECT
```
[[ $(ojod q staking validator $(ojod keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(ojod status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```
### GET LIVE PEERS
```
curl -sS http://localhost:15057/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```
### SET MINIMUM GAS PRICE
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uojo\"/" $HOME/.ojo/config/app.toml
```
### ENABLE PROMETHEUS
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.ojo/config/config.toml
```
### RESET CHAIN DATA
```
ojod tendermint unsafe-reset-all --keep-addr-book --home $HOME/.ojo --keep-addr-book
```
### REMOVE NODE
**Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your `priv_validator_key.json`!
**
```
cd $HOME
sudo systemctl stop ojo.service
sudo systemctl disable ojo.service
sudo rm /etc/systemd/system/ojo.service
sudo systemctl daemon-reload
rm -f $(which ojod)
rm -rf $HOME/.ojo
rm -rf $HOME/ojo
```
# ⚙️ Service Management
### RELOAD SERVICE CONFIGURATION
```
sudo systemctl daemon-reload
```
### ENABLE SERVICE
```
sudo systemctl enable ojo.service
```
### DISABLE SERVICE
```
sudo systemctl disable ojo.service
```
### START SERVICE
```
sudo systemctl start ojo.service
```
### STOP SERVICE
```
sudo systemctl stop ojo.service
```
### RESTART SERVICE
```
sudo systemctl restart ojo.service
```
### CHECK SERVICE STATUS
```
sudo systemctl status ojo.service
```
### CHECK SERVICE LOGS
```
sudo journalctl -u ojo.service -f --no-hostname -o cat
```
