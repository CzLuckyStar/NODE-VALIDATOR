
# AIRchains

Run the node using systemd

## Chain ID: `junction`

## Recommended Hardware Requirements

|   SPEC      |       Minimum          |
| :---------: | :-----------------------:|
|   **CPU**   |        2 Cores           |
|   **RAM**   |        4 GB              |
| **Storage** |    100 GB SSD or Nvme    |

|   SPEC      |       Recommend          |
| :---------: | :-----------------------:|
|   **CPU**   |        4 Cores           |
|   **RAM**   |        8 GB              |
| **Storage** |    200 GB SSD or Nvme    |


## Update and install packages for compiling
Install required packages
```
sudo apt update && sudo apt upgrade -y
sudo apt install make curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```
Install Go
```
cd $HOME && \
ver="1.22.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
### Build binary
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin
```

### Initialize the Node with the Moniker
Replace <moniker> with your own moniker
```
junctiond init <moniker>
```

### Download Genesis
```
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/genesis.json
cp genesis.json $HOME/.junction/config/genesis.json
```

### Configure
```
SEEDS="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656"
PEERS="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.junction/config/config.toml
```
### Set Pruning, Enable Prometheus, Gas Price, and Indexer
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"
sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.junction/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.junction/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.junction/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00025amf\"/" $HOME/.junction/config/app.toml
```
### Create service
```
sudo tee /etc/systemd/system/junctiond.service > /dev/null << EOF
[Unit]
Description=Airchain Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/local/bin/junctiond start
[Install]
WantedBy=multi-user.target
EOF
```

### System reload
``` 
sudo systemctl daemon-reload 
```

### Start service
```
sudo service junctiond start
```

### Start Node
```
junctiond start
```
### Check logs
```
sudo journalctl -fu junctiond
```
### Check Node status
```
junctiond status
```
Do not proceed with further steps until this process is finished. 'catching_up' field MUST return 'false'

## Create new wallet
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

Check wallet balances:
```
junctiond q bank balances <yourwallet>
```

Create VALIDATOR:
```
junctiond tx staking create-validator $HOME/.junction/config/validator.json --from wallet --chain-id junction --gas-prices 0.00025amf --gas-adjustment 1.5 --gas auto -y
```
### COMMAND
Check validator status:
```
junctiond status 2>&1 | jq .ValidatorInfo
```
```
junctiond status info
```
Delegate to Yourself
```
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a) 0.1amf  --from wallet --chain-id junction --gas-prices=0.00025amf  --gas-adjustment 1.5 --gas "auto" -y 
```

