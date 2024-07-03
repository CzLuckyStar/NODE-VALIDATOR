
# 🔑 Key management
### ADD NEW KEY
```
wardend keys add wallet
```
### RECOVER EXISTING KEY
```
wardend keys add wallet --recover
```
### LIST ALL KEYS
```
wardend keys list
```
### DELETE KEY
```
wardend keys delete wallet
```
### EXPORT KEY TO A FILE
```
wardend keys export wallet
```
### IMPORT KEY FROM A FILE
```
wardend keys import wallet wallet.backup
```
### QUERY WALLET BALANCE
```
wardend q bank balances $(wardend keys show wallet -a)
```
# 👷 Validator management
Please make sure you have adjusted moniker, identity, details and website to match your values.

### CREATE NEW VALIDATOR
```
wardend tx staking create-validator \
--amount 1000000uward \
--pubkey $(wardend tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id buenavista-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.0025uward \
-y
```
### EDIT EXISTING VALIDATOR
```
wardend tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id buenavista-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.0025uward \
-y
```
### UNJAIL VALIDATOR
```
wardend tx slashing unjail --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### JAIL REASON
```
wardend query slashing signing-info $(wardend tendermint show-validator)
```
### LIST ALL ACTIVE VALIDATORS
```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
### LIST ALL INACTIVE VALIDATORS
```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
### VIEW VALIDATOR DETAILS
```
wardend q staking validator $(wardend keys show wallet --bech val -a)
```
# 💲 Token management
### WITHDRAW REWARDS FROM ALL VALIDATORS
```
wardend tx distribution withdraw-all-rewards --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR
```
wardend tx distribution withdraw-rewards $(wardend keys show wallet --bech val -a) --commission --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### DELEGATE TOKENS TO YOURSELF
```
wardend tx staking delegate $(wardend keys show wallet --bech val -a) 1000000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### DELEGATE TOKENS TO VALIDATOR
```
wardend tx staking delegate <TO_VALOPER_ADDRESS> 1000000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### REDELEGATE TOKENS TO ANOTHER VALIDATOR
```
wardend tx staking redelegate $(wardend keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### UNBOND TOKENS FROM YOUR VALIDATOR
```
wardend tx staking unbond $(wardend keys show wallet --bech val -a) 1000000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### SEND TOKENS TO THE WALLET
```
wardend tx bank send wallet <TO_WALLET_ADDRESS> 1000000uward --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
# 🗳 Governance
### LIST ALL PROPOSALS
```
wardend query gov proposals
```
### VIEW PROPOSAL BY ID
```
wardend query gov proposal 1
```
### VOTE ‘YES’
```
wardend tx gov vote 1 yes --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### VOTE ‘NO’
```
wardend tx gov vote 1 no --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### VOTE ‘ABSTAIN’
```
wardend tx gov vote 1 abstain --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
### VOTE ‘NOWITHVETO’
```
wardend tx gov vote 1 NoWithVeto --from wallet --chain-id buenavista-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0025uward -y
```
# ⚡️ Utility
### UPDATE PORTS
```
CUSTOM_PORT=110
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.warden/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.warden/config/app.toml
```
### UPDATE INDEXER
Disable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.warden/config/config.toml
```
Enable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.warden/config/config.toml
```
### UPDATE PRUNING
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.warden/config/app.toml
```
# 🚨 Maintenance
### GET VALIDATOR INFO
```
wardend status 2>&1 | jq .ValidatorInfo
```
### GET SYNC INFO
```
wardend status 2>&1 | jq .SyncInfo
```
### GET NODE PEER
```
echo $(wardend tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.warden/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```
### CHECK IF VALIDATOR KEY IS CORRECT
```
[[ $(wardend q staking validator $(wardend keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(wardend status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```
### GET LIVE PEERS
```
curl -sS http://localhost:17857/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```
### SET MINIMUM GAS PRICE
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uward\"/" $HOME/.warden/config/app.toml
```
### ENABLE PROMETHEUS
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.warden/config/config.toml
```
### RESET CHAIN DATA
```
wardend tendermint unsafe-reset-all --keep-addr-book --home $HOME/.warden --keep-addr-book
```
### REMOVE NODE
**Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!**
```
cd $HOME
sudo systemctl stop warden-testnet.service
sudo systemctl disable warden-testnet.service
sudo rm /etc/systemd/system/warden-testnet.service
sudo systemctl daemon-reload
rm -f $(which wardend)
rm -rf $HOME/.warden
rm -rf $HOME/wardenprotocol
```
# ⚙️ Service Management
### RELOAD SERVICE CONFIGURATION
```
sudo systemctl daemon-reload
```
### ENABLE SERVICE
```
sudo systemctl enable warden-testnet.service
```
### DISABLE SERVICE
```
sudo systemctl disable warden-testnet.service
```
### START SERVICE
```
sudo systemctl start warden-testnet.service
```
### STOP SERVICE
```
sudo systemctl stop warden-testnet.service
```
### RESTART SERVICE
```
sudo systemctl restart warden-testnet.service
```
### CHECK SERVICE STATUS
```
sudo systemctl status warden-testnet.service
```
### CHECK SERVICE LOGS
```
sudo journalctl -u warden-testnet.service -f --no-hostname -o cat
```
