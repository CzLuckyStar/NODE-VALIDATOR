### CHECK SYNC info
```
junctiond status 2>&1 | jq .sync_info
```
### CHECK SYNC
```
junctiond status 2>&1 | jq .SyncInfo.catching_up
```
### CHECK LOG:
```
sudo journalctl -u junctiond -f --no-hostname -o cat
```
### Reload Service
```
sudo systemctl daemon-reload
```
### Enable Service
```
sudo systemctl enable junctiond
```
### Disable Service
```
sudo systemctl disable junctiond
```
### Start Service
```
sudo systemctl start junctiond
```
### Stop Service
```
sudo systemctl stop junctiond
```
### Restart Service
```
sudo systemctl restart junctiond
```
### Check Service Status
```
sudo systemctl status junctiond
```
### Check Service Logs
```
sudo journalctl -u junctiond -f --no-hostname -o cat
```

### Create new wallet
```
junctiond keys add <new-wallet>
```
This command will generate your wallet's mnemonic and address. It's crucial to write these down and store them securely.

### Create new validator

```
junctiond comet show-validator
```
The output will be something like this:
```
{"@type":"/cosmos.crypto.ed25519.PubKey","key":"ZXONS7NNjLWH4HePBOoHKDAYeLXQO5iUwpCRQSi1poI="}
```
Create validator file
```
nano $HOME/.junction/config/validator.json
```
Input data to validator.json file. Replace at "pubkey": {....} from show-validator output
```
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"ZXONS7NNjLWH4HePBOoHKDAYeLXQO5iUwpCRQSi1poI="},
	"amount": "1000000amf",
	"moniker": "<validator-name>",
	"identity": "optional identity signature (ex. UPort or Keybase)",
	"website": "validator's (optional) website",
	"security": "validator's (optional) security contact email",
	"details": "validator's (optional) details",
	"commission-rate": "0.05",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
```
Ctrl+o > Enter > Ctrl+x to save file & exit

Create VALIDATOR:
```
junctiond tx staking create-validator $HOME/.junction/config/validator.json --from wallet --chain-id junction --gas-prices 0.00025amf --gas-adjustment 1.5 --gas auto -y
```

### OR:
```
junctiond tx staking create-validator \
  --amount "1000000amf" \
  --pubkey $(junctiond tendermint show-validator) \
  --moniker "MONIKER" \
  --identity "KEYBASE_ID" \
  --details "YOUR DETAILS" \
  --website "YOUR WEBSITE" \
  --chain-id junction \
  --commission-rate "0.05" \
  --commission-max-rate "0.20" \
  --commission-max-change-rate "0.01" \
  --min-self-delegation "1" \
  --gas-prices "0.00025amf " \
  --gas "auto" \
  --gas-adjustment "1.5" \
  --from wallet \
  -y
```

A prompt will appear in the CLI. To proceed, type 'y' and press enter.

Key management
Add New Wallet
```
junctiond keys add wallet
```
Restore executing wallet
```
junctiond keys add wallet --recover
```
List All Wallets
```
junctiond keys list
```
Delete wallet
```
junctiond keys delete wallet
```
Check Balance
```
junctiond q bank balances $(junctiond keys show wallet -a)
```
Export Key (save to wallet.backup)
```
junctiond keys export wallet
```
Import Key (restore from wallet.backup)
```
junctiond keys import wallet wallet.backup
```
Withdraw all rewards
```
junctiond tx distribution withdraw-all-rewards --from wallet --chain-id junction --gas auto --gas-adjustment 1.5
```
Withdraw rewards and commission from your validator
```
junctiond tx distribution withdraw-rewards $(junctiond keys show wallet --bech val -a) --commission --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Delegate to Yourself
```
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a) 0.1amf  --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Delegate
```
junctiond tx staking delegate <TO_VALOPER_ADDRESS> 0.1amf  --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Redelegate Stake to Another Validator
```
junctiond tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 0.1amf  --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Unbond
```
junctiond tx staking unbond $(junctiond keys show wallet --bech val -a) 0.1amf  --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Transfer Funds
```
junctiond tx bank send wallet_ADDRESS <TO_WALLET_ADDRESS> 0.1amf  --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```

Edit Existing Validator
```
junctiond tx staking edit-validator \
--commission-rate 0.05 \
--new-moniker "$MONIKER" \
--identity "" \
--details "" \
--gas-prices "0.00025amf " \
--gas "auto" \
--gas-adjustment "1.5" \
--from wallet \
-y
```
Validator info
```
junctiond status 2>&1 | jq .ValidatorInfo
```
Get sync status
```
junctiond status 2>&1 | jq .SyncInfo.catching_up
```
**CHECK MISSING BLOCK:**
```
junctiond query slashing signing-info $(junctiond tendermint show-validator)
```

Get latest height
```
junctiond status 2>&1 | jq .SyncInfo.latest_block_height
```
Validator Details
```
junctiond q staking validator $(junctiond keys show wallet --bech val -a)
```
Jailing info
```
junctiond q slashing signing-info $(junctiond tendermint show-validator)
```
Unjail validator
```
junctiond tx slashing unjail --from wallet --chain-id junction --gas-prices=0.005 0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Active Validators List
```
junctiond q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```
Check Validator key
```
[[ $(junctiond q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(junctiond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```
Signing info
```
junctiond q slashing signing-info $(junctiond tendermint show-validator)
```
```
junctiond  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1amf  \
--type Text \
--gas-prices "0.00025amf " \
--gas "auto" \
--gas-adjustment "1.5" \
--from wallet \
-y
```
🗳 Governance
List all proposals
```
junctiond query gov proposals
```
View proposal by id
```
junctiond query gov proposal 1
```
Vote 'Yes'
```
junctiond tx gov vote 78 yes --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Vote 'No'
```
junctiond tx gov vote 1 no --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Vote 'Abstain'
```
junctiond tx gov vote 1 abstain --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Vote 'NoWithVeto'
```
junctiond tx gov vote 1 nowithveto --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```
Remove node
```
sudo systemctl stop junctiond
sudo systemctl disable junctiond
sudo rm /etc/systemd/system/junctiond.service
sudo systemctl daemon-reload
rm -f $(which junctiond)
rm -rf $HOME/.junction
```
