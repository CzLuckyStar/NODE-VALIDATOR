# Useful commands
Useful set of commands for node operators. From key management to chain governance.


Fairblock Network
Daemon configuration
```
fairyringd config node https://testnet-fairyring-rpc.lavenderfive.com:443
fairyringd config chain-id fairyring-testnet-1
```
# 🔑 Key management
Add new key
```
fairyringd keys add wallet
```
Recover existing key
```
fairyringd keys add wallet --recover
```
List all keys
```
fairyringd keys list
```
Delete key
```
fairyringd keys delete wallet
```
Export key to the file
```
fairyringd keys export wallet
```
Import key from the file
```
fairyringd keys import wallet wallet.backup
```
Query wallet balance
```
fairyringd q bank balances $(fairyringd keys show wallet -a)
```
# 👷 Validator management
Please make sure you have adjusted moniker, identity, details and website to match your values.

Create new validator
```
fairyringd tx staking create-validator \
--amount  \
--pubkey $(fairyringd tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id fairyring-testnet-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices  \
-y
```
Edit existing validator
```
fairyringd tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id fairyring-testnet-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices  \
-y
```
Unjail validator
```
fairyringd tx slashing unjail --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Jail reason
```
fairyringd query slashing signing-info $(fairyringd tendermint show-validator)
```
List all active validators
```
fairyringd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
List all inactive validators
```
fairyringd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
View validator details
```
fairyringd q staking validator $(fairyringd keys show wallet --bech val -a)
```
# 💲 Token management
Withdraw rewards from all validators
```
fairyringd tx distribution withdraw-all-rewards --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Withdraw commission and rewards from your validator
```
fairyringd tx distribution withdraw-rewards $(fairyringd keys show wallet --bech val -a) --commission --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Delegate tokens to yourself
```
fairyringd tx staking delegate $(fairyringd keys show wallet --bech val -a)  --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Delegate tokens to validator
```
fairyringd tx staking delegate <TO_VALOPER_ADDRESS>  --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Redelegate tokens to another validator
```
fairyringd tx staking redelegate $(fairyringd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS>  --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Unbond tokens from your validator
```
fairyringd tx staking unbond $(fairyringd keys show wallet --bech val -a)  --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Send tokens to the wallet
```
fairyringd tx bank send wallet <TO_WALLET_ADDRESS>  --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
# 🗳 Governance
List all proposals
```
fairyringd query gov proposals
```
View proposal by id
```
fairyringd query gov proposal 1
```
Vote 'Yes'
```
fairyringd tx gov vote 1 yes --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Vote 'No'
```
fairyringd tx gov vote 1 no --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Vote 'Abstain'
```
fairyringd tx gov vote 1 abstain --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
Vote 'NoWithVeto'
```
fairyringd tx gov vote 1 NoWithVeto --from wallet --chain-id fairyring-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices  -y
```
# ⚡️ Utility
Update ports
```
CUSTOM_PORT=247
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" .fairyring/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" .fairyring/config/app.toml
```
Update Indexer
Disable indexer

```
sed -i -e 's|^indexer *=.*|indexer = "null"|' .fairyring/config/config.toml
```
Enable indexer

```
sed -i -e 's|^indexer *=.*|indexer = "kv"|' .fairyring/config/config.toml
```
Update pruning
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "107"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "13"|' \
  .fairyring/config/app.toml
```
# 🚨 Maintenance
Get validator info
```
fairyringd status 2>&1 | jq .ValidatorInfo
```
Get sync info
```
fairyringd status 2>&1 | jq .SyncInfo
```
Get node peer
```
echo $(fairyringd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat .fairyring/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```
Check if validator key is correct
```
[[ $(fairyringd q staking validator $(fairyringd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(fairyringd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```
Get live peers
```
curl -sS http://localhost:24757/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```
Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"\"/" .fairyring/config/app.toml
```
Enable prometheus
```
sed -i -e "s/prometheus = false/prometheus = true/" .fairyring/config/config.toml
```
Reset chain data
```
fairyringd tendermint unsafe-reset-all --home .fairyring --keep-addr-book
```
Remove node
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!

```
cd $HOME
sudo systemctl stop fairyringd
sudo systemctl disable fairyringd
sudo rm /etc/systemd/system/fairyringd.fairyringd
sudo systemctl daemon-reload
rm -f $(which fairyringd)
rm -rf .fairyring
rm -rf $HOME/osmosis
```
# ⚙️ Service Management
Reload fairyringd configuration
```
sudo systemctl daemon-reload
```
Enable fairyringd
```
sudo systemctl enable fairyringd
```
Disable fairyringd
```
sudo systemctl disable fairyringd
```
Start fairyringd
```
sudo systemctl start fairyringd
```
Stop fairyringd
```
sudo systemctl stop fairyringd
```
Restart fairyringd
```
sudo systemctl restart fairyringd
```
Check fairyringd status
```
sudo systemctl status fairyringd
```
Check fairyringd logs
```
sudo journalctl -u fairyringd -f --no-hostname -o cat
```
