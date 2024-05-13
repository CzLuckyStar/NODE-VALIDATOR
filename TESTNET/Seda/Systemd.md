
# SEDA 

Run the node using systemd

## Chain ID: `seda`

## Recommended Hardware Requirements

|   SPEC      |       Minimum             |
| :---------: | :------------------------:|
|   **CPU**   |        NA Cores           |
|   **RAM**   |        32 GB              |
| **Storage** |    1TB SSD or Nvme        |

|   SPEC      |       Recommend           |
| :---------: | :------------------------:|
|   **CPU**   |        NA Cores           |
|   **RAM**   |        NA GB              |
| **Storage** |    NA GB SSD or Nvme      |


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
wget -O sedad https://github.com/sedaprotocol/seda-chain/releases/download/v0.1.1/sedad-amd64
chmod +x sedad
sudo mv sedad /usr/local/bin
```

### Initialize the Node with the Moniker
Replace <moniker> with your own moniker
```
sedad init <moniker>
```

### Download Genesis
dev
```
wget https://github.com/sedaprotocol/seda-networks/blob/main/devnet/genesis.json
cp genesis.json $HOME/.sedad/config/genesis.json
```
testnet
```
wget https://github.com/sedaprotocol/seda-networks/blob/main/testnet/genesis.json
cp genesis.json $HOME/.sedad/config/genesis.json
```

### Configure
dev
```
SEEDS="5f9f30d597f6a8f9a1e42385143a771e3a8a4e1b@35.177.180.184:26656,306d36164a13eee3fa3b1f673a106d05f9c774e8@18.130.31.180:26656"
PEERS="5f9f30d597f6a8f9a1e42385143a771e3a8a4e1b@35.177.180.184:26656,306d36164a13eee3fa3b1f673a106d05f9c774e8@18.130.31.180:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.sedad/config/config.toml
```
testnet
```
SEEDS="cb75c263cff51a14a4f10694046bb81414d10064@18.171.36.35:26656,9b6de59e38faa31ac0f2ae2469954be562fc167f@13.41.125.154:26656"
PEERS="cb75c263cff51a14a4f10694046bb81414d10064@18.171.36.35:26656,9b6de59e38faa31ac0f2ae2469954be562fc167f@13.41.125.154:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.sedad/config/config.toml
```
### Set Pruning, Enable Prometheus, Gas Price, and Indexer
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"
sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.sedad/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.sedad/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.sedad/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.sedad/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.sedad/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.005aseda\"/" $HOME/.sedad/config/app.toml
```
### Create service
```
sudo tee /etc/systemd/system/sedad.service > /dev/null << EOF
[Unit]
Description=Seda Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/local/bin/sedad start
[Install]
WantedBy=multi-user.target
EOF
```
start service:
```
sudo systemctl daemon-reload
sudo systemctl enable sedad
```
```
sudo systemctl restart sedad && journalctl -f -u sedad
```

### System reload
``` 
sudo systemctl daemon-reload 
```

### Start service
```
sudo service sedad start
```

### Start Node
```
sedad start
```
### Check logs
```
sudo journalctl -fu sedad
```
### Check Node status
```
sedad status
```
Do not proceed with further steps until this process is finished. 'catching_up' field MUST return 'false'

## Create new wallet
```
sedad keys add <new-wallet>
```
This command will generate your wallet's mnemonic and address. It's crucial to write these down and store them securely.

### Create new validator

```
sedad comet show-validator
```
The output will be something like this:
```
{"@type":"/cosmos.crypto.ed25519.PubKey","key":"ZXONS7NNjLWH4HePBOoHKDAYeLXQO5iUwpCRQSi1poI="}
```
Create validator file
```
nano $HOME/.seda/config/validator.json
```
Input data to validator.json file. Replace at "pubkey": {....} from show-validator output
```
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"ZXONS7NNjLWH4HePBOoHKDAYeLXQO5iUwpCRQSi1poI="},
	"amount": "1000000aseda",
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
sedad q bank balances <yourwallet>
```

Create VALIDATOR:
```
sedad tx staking create-validator $HOME/.sedad/config/validator.json --from wallet --chain-id test-chain-PIfKS9 --gas-prices 0.005aseda --gas-adjustment 1.5 --gas auto -y
```
### COMMAND
Check validator status:
```
sedad status 2>&1 | jq .ValidatorInfo
```
```
sedad status info
```
Delegate to Yourself
```
sedad tx staking delegate $(sedad keys show wallet --bech val -a) 0.1amf  --from wallet --chain-id test-chain-PIfKS9 --gas-prices=0.005aseda  --gas-adjustment 1.5 --gas "auto" -y 
```
