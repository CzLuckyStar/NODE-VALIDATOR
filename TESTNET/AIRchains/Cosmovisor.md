# AIRCHAINS

RPC: `https://airchains-test.rpc.moonbridge.team`

Chain ID: `junction` | Latest Version: `v0.1.0` | Custom Port: `137`

Specify the name of your `“MONIKER”` (validator) which will be visible in the explorer
```
MONIKER="YOUR_MONIKER_NAME"
```
## Update system and install tools:
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git jq lz4 ncdu bashtop -y
```
### Install GO:
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

### Install Cosmovisor:
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

### Download and install:
```
cd $HOME
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond $HOME/go/bin/
junctiond version --long | grep -e commit -e version
```
## Config and Init node:

### Set node configuration:

```
junctiond init $MONIKER --chain-id junction && \
junctiond config set client chain-id junction && \
junctiond config set client keyring-backend test && \
junctiond config set client node tcp://localhost:13757
```

### Download genesis and addrbook
```
curl -Ls https://snapshots.moonbridge.team/testnet/airchains/genesis.json > $HOME/.junction/config/genesis.json
curl -Ls https://snapshots.moonbridge.team/testnet/airchains/addrbook.json > $HOME/.junction/config/addrbook.json
```
### Set seeds and peers:
```
SEEDS=""
PEERS="48887cbb310bb854d7f9da8d5687cbfca02b9968@35.200.245.190:26656,2d1ea4833843cc1433e3c44e69e297f357d2d8bd@5.78.118.106:26656,de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,1918bd71bc764c71456d10483f754884223959a5@35.240.206.208:26656,ddd9aade8e12d72cc874263c8b854e579903d21c@178.18.240.65:26656,eb62523dfa0f9bd66a9b0c281382702c185ce1ee@38.242.145.138:26656,0305205b9c2c76557381ed71ac23244558a51099@162.55.65.162:26656,086d19f4d7542666c8b0cac703f78d4a8d4ec528@135.148.232.105:26656,3e5f3247d41d2c3ceeef0987f836e9b29068a3e9@168.119.31.198:56256,8b72b2f2e027f8a736e36b2350f6897a5e9bfeaa@131.153.232.69:26656,6a2f6a5cd2050f72704d6a9c8917a5bf0ed63b53@93.115.25.41:26656,e09fa8cc6b06b99d07560b6c33443023e6a3b9c6@65.21.131.187:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.junction/config/config.toml
```
### Setting minimum gas price:
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.001amf\"|" $HOME/.junction/config/app.toml
```
### Setting pruning:
```
sed -i -e 's|^pruning *=.*|pruning = "custom"|' $HOME/.junction/config/app.toml
sed -i -e 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|' $HOME/.junction/config/app.toml
sed -i -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' $HOME/.junction/config/app.toml
sed -i -e 's|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.junction/config/app.toml
```
### Disable indexer:
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.junction/config/config.toml
```
### Enable Prometheus:
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.junction/config/config.toml
```
### Setting custom ports:
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:13758\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://0.0.0.0:13757\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:13760\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:13756\"%; s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):13756\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":13766\"%" $HOME/.junction/config/config.toml
sed -i -e "s%:1317%:13717%g; s%:8080%:13780%g; s%:9090%:13790%g; s%:9091%:13791%g; s%:8545%:13745%g; s%:8546%:13746%g; s%:6065%:13765%g" $HOME/.junction/config/app.toml
```
### Configure Cosmovisor:
```
mkdir -p $HOME/.junction/cosmovisor/genesis/bin
mkdir -p $HOME/.junction/cosmovisor/upgrades
cp $HOME/go/bin/junctiond $HOME/.junction/cosmovisor/genesis/bin/
```
### Create service:
```
sudo tee /etc/systemd/system/junctiond.service > /dev/null << EOF
[Unit]
Description=Airchains Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=always
RestartSec=5
LimitNOFILE=65535
Environment="DAEMON_NAME=junctiond"
Environment="DAEMON_HOME=$HOME/.junction"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable junctiond
```
### Download latest snapshot:
```
curl -o - -L https://snapshots.moonbridge.team/testnet/airchains/snapshot_latest.tar.lz4 | lz4 -dc - | tar -x -C $HOME/.junction
[[ -f $HOME/.junction/data/upgrade-info.json ]] && cp $HOME/.junction/data/upgrade-info.json $HOME/.junction/cosmovisor/genesis/upgrade-info.json
```

### Start service and check the logs:
```
sudo systemctl start junctiond && sudo journalctl -u junctiond -f --no-hostname -o cat
```
### Create wallet:
```
junctiond keys add wallet
```

### Check wallet balance:
```
junctiond q bank balances $(junctiond keys show wallet -a)
```

### Create a validator.json:

Get pubkey
```
junctiond comet show-validator
```
```
cd $HOME/.junction/config
nano validator.json

{
  "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="},
  "amount": "1000000amf",
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

### Create validator:
```
junctiond tx staking create-validator $HOME/.junction/config/validator.json \
  --from wallet \
  --chain-id junction \
  --fees 500amf \
  -y
```

## Delete node:
```
sudo systemctl stop junctiond
sudo systemctl disable junctiond
sudo rm -rf /etc/systemd/system/junctiond.service
sudo systemctl daemon-reload
sudo rm -f $(which junctiond)
sudo rm -rf $HOME/.junction
```
# END
