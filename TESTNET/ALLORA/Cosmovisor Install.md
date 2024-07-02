# ALLORA

## Install

RPC: `https://allora-test.rpc.moonbridge.team`

Chain ID: `testnet` | Latest Version: `v0.0.10` | Custom Port: `125`

Specify the name of `your “MONIKER”` (validator) which will be visible in the explorer
```
MONIKER="YOUR_MONIKER_NAME"
```

## Update system and install tools
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git jq lz4 ncdu bashtop -y
```

### Install GO
```
cd $HOME
version="1.22.3"
wget "https://golang.org/dl/go$version.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$version.linux-amd64.tar.gz"
rm "go$version.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Install Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

## Download and install:
```
cd $HOME
git clone https://github.com/allora-network/allora-chain
cd allora-chain
git checkout v0.0.10
make install
allorad version --long | grep -e commit -e version
```

## Config and Init node
### Set node configuration
```
allorad config chain-id testnet
allorad config keyring-backend test
allorad config node tcp://localhost:12557
```
### Initialize node
```
allorad init $MONIKER --chain-id testnet
```
### Download genesis and addrbook
```
curl -Ls https://snapshots.moonbridge.team/testnet/allora/genesis.json > $HOME/.allorad/config/genesis.json
curl -Ls https://snapshots.moonbridge.team/testnet/allora/addrbook.json > $HOME/.allorad/config/addrbook.json
```
### Set seeds and peers
```
SEEDS=""
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.allorad/config/config.toml
```
### Setting minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0uallo\"|" $HOME/.allorad/config/app.toml
```
### Setting pruning
```
sed -i -e 's|^pruning *=.*|pruning = "custom"|' $HOME/.allorad/config/app.toml
sed -i -e 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|' $HOME/.allorad/config/app.toml
sed -i -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' $HOME/.allorad/config/app.toml
sed -i -e 's|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.allorad/config/app.toml
```
### Disable indexer
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.allorad/config/config.toml
```
### Enable Prometheus
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.allorad/config/config.toml
```
### Setting custom ports
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:12558\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://0.0.0.0:12557\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:12560\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:12556\"%; s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):12556\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":12566\"%" $HOME/.allorad/config/config.toml
sed -i -e "s%:1317%:12517%g; s%:8080%:12580%g; s%:9090%:12590%g; s%:9091%:12591%g; s%:8545%:12545%g; s%:8546%:12546%g; s%:6065%:12565%g" $HOME/.allorad/config/app.toml
```
## Configure Cosmovisor
```
mkdir -p $HOME/.allorad/cosmovisor/genesis/bin
mkdir -p $HOME/.allorad/cosmovisor/upgrades
cp $HOME/go/bin/allorad $HOME/.allorad/cosmovisor/genesis/bin/
```

### Create service
```
sudo tee /etc/systemd/system/allorad.service > /dev/null << EOF
[Unit]
Description=Allora Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=always
RestartSec=5
LimitNOFILE=65535
Environment="DAEMON_NAME=allorad"
Environment="DAEMON_HOME=$HOME/.allorad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable allorad
```
## Download latest snapshot
```
curl -o - -L https://snapshots.moonbridge.team/testnet/allora/snapshot_latest.tar.lz4 | lz4 -dc - | tar -x -C $HOME/.allorad
[[ -f $HOME/.allorad/data/upgrade-info.json ]] && cp $HOME/.allorad/data/upgrade-info.json $HOME/.allorad/cosmovisor/genesis/upgrade-info.json
```
### Start service and check the logs
```
sudo systemctl start allorad && sudo journalctl -u allorad -f --no-hostname -o cat
```
### Create wallet
```
allorad keys add wallet
```
### Check wallet balance
```
allorad q bank balances $(allorad keys show wallet -a)
```
## Create a validator.json
### Get pubkey
```
allorad comet show-validator
```
```
cd $HOME/.allorad/config
nano validator.json
```
```
{
  "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="},
  "amount": "1000000uallo",
  "moniker": "YOUR_MONIKER_NAME",
  "identity": "YOUR_KEYBASE_ID",
  "details": "YOUR_DETAILS",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_EMAIL_ADDRESS",
  "commission-rate": "0.10",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.01",
  "min-self-delegation": "1"
}
```
## Create validator
```
allorad tx staking create-validator $HOME/.allorad/config/validator.json \
  --chain-id testnet \
  --keyring-backend=test \
  --from wallet \
  -y
```
## Delete node
```
sudo systemctl stop allorad
sudo systemctl disable allorad
sudo rm -rf /etc/systemd/system/allorad.service
sudo systemctl daemon-reload
sudo rm -f $(which allorad)
sudo rm -rf $HOME/.allorad
sudo rm -rf $HOME/allora-chain
```


# END
