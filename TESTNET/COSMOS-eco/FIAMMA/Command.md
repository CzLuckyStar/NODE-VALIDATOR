
# COMMAND

## Service operations ⚙️
### Check logs
```
sudo journalctl -u fiammad -f
```

### Start service
```
sudo systemctl start fiammad
```

### Stop service
```
sudo systemctl stop fiammad
```

### Restart service
```
sudo systemctl restart fiammad
```

### Check service status
```
sudo systemctl status fiammad
```

### Reload services
```
sudo systemctl daemon-reload
```

### Enable Service
```
sudo systemctl enable fiammad
```

### Disable Service
```
sudo systemctl disable fiammad
```

### Node info
```
fiammad status 2>&1 | jq
```

### Your node peer
```
echo $(fiammad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.fiamma/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

## Key management

### Add New Wallet
```
fiammad keys add $WALLET
```

### Restore executing wallet
```
fiammad keys add $WALLET --recover
```

### List All Wallets
```
fiammad keys list
```

### Delete wallet
```
fiammad keys delete $WALLET
```

### Check Balance
```
fiammad q bank balances $WALLET_ADDRESS 
```

### Export Key (save to wallet.backup)
```
fiammad keys export $WALLET
```

### View EVM Prived Key
```
fiammad keys unsafe-export-eth-key $WALLET
```

### Import Key (restore from wallet.backup)
```
fiammad keys import $WALLET wallet.backup
```

## Tokens

To valoper address

To wallet address

Amount, ufia

1000000

### Withdraw all rewards
```
fiammad tx distribution withdraw-all-rewards --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 
```

### Withdraw rewards and commission from your validator
```
fiammad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

### Check your balance
```
fiammad query bank balances $WALLET_ADDRESS
```

### Delegate to Yourself
```
fiammad tx staking delegate $(fiammad keys show $WALLET --bech val -a) 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

### Delegate
```
fiammad tx staking delegate <TO_VALOPER_ADDRESS> 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 	
```

### Redelegate Stake to Another Validator
```
fiammad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

### Unbond
```
fiammad tx staking unbond $(fiammad keys show $WALLET --bech val -a) 1000000ufia --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

### Transfer Funds
```
fiammad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000ufia --gas auto --gas-adjustment 1.5 -y 
```

## Validator operations

Moniker

Identity

Details: I love Fiamma

Amount: 1000000 ufia



Commission rate:  0.1

Commission max rate: 0.2

Commission max change rate: 0.01

### Create New Validator
```
fiammad tx staking create-validator \
--amount 1000000ufia \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(fiammad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love Fiamma" \
--chain-id fiamma-testnet-1 \
--gas auto --gas-adjustment 1.5 \
-y 
```

### Edit Existing Validator
```
fiammad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love Fiamma" \
--from $WALLET \
--chain-id fiamma-testnet-1 \
--fees=500ufia \
--gas auto --gas-adjustment 1.5 \
-y 
```

### Validator info
```
fiammad status 2>&1 | jq
```

### Validator Details
```
fiammad q staking validator $(fiammad keys show $WALLET --bech val -a) 
```

### Jailing info
```
fiammad q slashing signing-info $(fiammad tendermint show-validator) 
```

### Slashing parameters
```
fiammad q slashing params 
```

### Unjail validator
```
fiammad tx slashing unjail --from $WALLET --chain-id fiamma-testnet-1 --gas auto --gas-adjustment 1.5 -y 
```

### Active Validators List
```
fiammad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

### Check Validator key
```
[[ $(fiammad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(fiammad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

### Signing info
```
fiammad q slashing signing-info $(fiammad tendermint show-validator) 
```

## Governance

Title

Description

Deposit: 1000000 ufia

### Create New Text Proposal
```
fiammad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000ufia \
--type Text \
--from $WALLET \
--gas auto --gas-adjustment 1.5 \
-y 
```


### Proposals List
```
fiammad query gov proposals 
```

Proposal ID
1

Proposal option: YesNoNo with vetoAbstain

### View proposal
```
fiammad query gov proposal 1 
```

### Vote
```
fiammad tx gov vote 1 yes --from $WALLET --chain-id fiamma-testnet-1  --gas auto --gas-adjustment 1.5 -y
```


# END
