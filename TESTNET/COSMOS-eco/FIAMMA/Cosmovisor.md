#  Installation

##  Hardware Requirements

Components	Minimum Requirements
CPU 4

RAM 8+ GB

STORAGE +200 GB SSD

##  Installation packages and dependencies
```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
sudo apt-get update && apt-get install -y libssl-dev
```

##  Go Installation
```
cd $HOME
VER="1.22.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

##  Install node

```
echo "export FIAMMA_PORT="37"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```
cd $HOME
rm -rf fiamma
git clone https://github.com/fiamma-chain/fiamma
cd fiamma
git checkout v0.1.3
make build
```

```
mkdir -p ~/.fiamma/cosmovisor/upgrades/0.1.3/bin
mv $HOME/fiamma/build/fiammad ~/.fiamma/cosmovisor/upgrades/0.1.3/bin/
```

```
sudo ln -s ~/.fiamma/cosmovisor/upgrades/0.1.3 ~/.fiamma/cosmovisor/current -f
sudo ln -s ~/.fiamma/cosmovisor/current/bin/fiammad /usr/local/bin/fiammad -f
```

```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

```
fiammad version --long tail
```


##  Create a service
```
sudo tee /etc/systemd/system/fiammad.service > /dev/null << EOF
[Unit]
Description=fiamma node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --home $HOME/.fiamma
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=${HOME}/.fiamma"
Environment="DAEMON_NAME=fiammad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:~/.fiamma/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```


##  Let's activate it
```
sudo systemctl daemon-reload
sudo systemctl enable fiammad
```

```
cd
```


##  Initialize the node
```
fiammad init NODE_NAME --chain-id fiamma-testnet-1
```

##  Genesis addrbook
```
curl https://raw.githubusercontent.com/fiamma-chain/networks/main/fiamma-testnet-1/genesis.json -o ~/.fiamma/config/genesis.json
curl https://raw.githubusercontent.com/MictoNode/fiamma-chain/main/addrbook.json -o ~/.fiamma/config/addrbook.json
```

##  Port
```
sed -i.bak -e "s%:1317%:${FIAMMA_PORT}317%g;
s%:8080%:${FIAMMA_PORT}080%g;
s%:9090%:${FIAMMA_PORT}090%g;
s%:9091%:${FIAMMA_PORT}091%g;
s%:8545%:${FIAMMA_PORT}545%g;
s%:8546%:${FIAMMA_PORT}546%g;
s%:6065%:${FIAMMA_PORT}065%g" $HOME/.fiamma/config/app.toml
```

```
sed -i.bak -e "s%:26658%:${FIAMMA_PORT}658%g;
s%:26657%:${FIAMMA_PORT}657%g;
s%:6060%:${FIAMMA_PORT}060%g;
s%:26656%:${FIAMMA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${FIAMMA_PORT}656\"%;
s%:26660%:${FIAMMA_PORT}660%g" $HOME/.fiamma/config/config.toml
```

##  Peers and Seeds
```
SEEDS="7f3988dc1f6254e664119d24b52982031e34327b@35.73.202.182:36656,40449ad696760c0d1b675c2741e846b5d08235a3@18.182.20.173:36656"
PEERS="5d6828849a45cf027e035593d8790bc62aca9cef@18.182.20.173:26656,526d13f3ce3e0b56fa3ac26a48f231e559d4d60c@35.73.202.182:26656,cedc2b0f16422718c320d17fc44935ad1c39e62d@172.31.26.39:26656,74cee55ba0696fcd75d637d0de637b7dfecb67bf@65.109.50.163:12656,21a5cae23e835f99735798024eef39fa0875bc62@65.109.30.110:17456,1833b283cbd1240e5a78c394f2d0955794e7732b@146.19.24.175:26856,4d56ef9164999825b886d67c95d9efbc12b455e9@65.109.49.47:29656,534bbf0712baf6665c2f3793131f7f53aa2806a6@193.70.47.69:26656,47c87d8ac9709669f7e9e9d9c8f5b76118763a13@94.130.216.221:26656,4ccfdc1ae7a8b87a83c0a675932960b750ea0e24@144.76.92.22:11656,4839dd83edd6d7cbb50974d4b1d748104ac56e58@65.109.112.148:40056,f1e941fa754357115f491dd1e138ac70610ab4a4@5.9.87.231:56656,9c87bf6872f2ca15c5f0b73348e6315be512aaa8@65.108.10.239:60956,781ff5ecf63b74c8b3934274ae3d4827ea5c4f74@65.109.57.212:29656,37e2b149db5558436bd507ecca2f62fe605f92fe@88.198.27.51:60556,67fffd28af7cc2a928c52fae6a09fe2812a6638d@217.66.20.45:26656,e00ecc7687e29f09f694dd4dd4a21988ce6a43f9@178.205.102.224:26656,a12e8531f345ccff39f47847aabf12e73e216ee3@144.76.97.251:26796,e2b57b310a6f3c4c0f85fc3dc3447d7e9696cd65@95.165.89.222:26706"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.fiamma/config/config.toml
```

##  Pruning
```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.fiamma/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.fiamma/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" $HOME/.fiamma/config/app.toml
```

##  Gas Settings
```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.00001ufia"|g' $HOME/.fiamma/config/app.toml
```

##  Indexer
```
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.fiamma/config/config.toml
```

##  Snap (soon)


##  Let's get started
```
sudo systemctl start fiammad && sudo journalctl -u fiammad -f -o cat
```

##  Log Command
```
journalctl -u fiammad -f -o cat
```


##  Create wallet
```
fiammad keys add wallet-name
```

don't forget to backup the wallet words!

##  Import wallet
```
fiammad keys add wallet-name --recover
```

##  Faucet

Faucet for Fiamma
website faucet
```
https://testnet-faucet.fiammachain.io/
```

Join the fiamma Discord Server!
Discord
$request fiamma-address


##  Create Validator
Reminder: You can't create a validator without Sync. You must have to catch the latest block.

```
cd $HOME
```

The code below will give you pubkey. Save it. You'll need it.

```
fiammad tendermint show-validator
```

```
nano validator.json
```

Copy the below directly. Make the changes and save (ctrl+x, y, enter).

```
{
        "pubkey": write-your-pubkey,
        "amount": "10000ufia",
        "moniker": "myvalidator",
        "identity": "optional identity signature (ex. UPort or Keybase)",
        "website": "validator's (optional) website",
        "security": "validator's (optional) security contact email",
        "details": "validator's (optional) details",
        "commission-rate": "0.1",
        "commission-max-rate": "0.2",
        "commission-max-change-rate": "0.01",
        "min-self-delegation": "1"
}
```

```
fiammad tx staking create-validator $HOME/validator.json \
    --from=wallet-name \
    --chain-id=fiamma-testnet-1 \
    --fees=500ufia \
    --node=http://localhost:(whatever you typed in custom_port)657
```

##  Delegate to Yourself
Find your valoper address with the code below

```
fiammad q staking validator $(fiammad keys show wallet-name --bech val -a) --node=http://localhost:(whatever you typed in custom_port)657
```

```
fiammad tx staking delegate valoper-address 10000ufia \
--chain-id fiamma-testnet-1 \
--from "wallet-name" \
--fees 500ufia \
--node=http://localhost:(whatever you typed in custom_port)657
```

##  Edit Validator
```
fiammad tx staking edit-validator \
--chain-id fiamma-testnet-1 \
--commission-rate 0.1 \
--new-moniker "validator-name" \
--identity "" \
--details "" \
--website "" \
--security-contact "" \
--from "wallet-name" \
--node http://localhost:(whatever you typed in custom_port)657 \
--fees 500ufia \
-y
```

##  Complete deletion
```
cd $HOME
sudo systemctl stop fiammad
sudo systemctl disable fiammad
sudo rm -rf /etc/systemd/system/fiammad.service
sudo systemctl daemon-reload
sudo rm -f /usr/local/bin/fiammad
sudo rm -f $(which fiamma)
sudo rm -rf $HOME/.fiamma $HOME/fiamma
sed -i "/FIAMMA_/d" $HOME/.bash_profile
```


##  Block check
```
local_height=$(curl -s localhost:(whatever you typed in custom_port)657/status | jq -r .result.sync_info.latest_block_height); network_height=$(curl -s https://fiamma-testnet-rpc.mictonode.com/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```

Your node height - the current block of your node

Network height - the last block of the network

Blocks left - how many blocks your node has left to sync.
