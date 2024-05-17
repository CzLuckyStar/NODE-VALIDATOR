# Fairblock Network
Description: Setting up your validator node has never been so easy. Get your validator running in minutes by following step by step instructions.
### Installation

Network name: Fairblock Network | Chain ID: fairyring-testnet-1 | Latest version: v0.5.0 | Custom Port: 247

Install dependencies

This guide assumes that you are using Ubuntu.

Upgrade your operating system:
```
sudo apt update && sudo apt upgrade -y
```
Install essential packages:
```
sudo apt install git curl tar wget libssl-dev jq build-essential gcc make
```
Download and install Go:
```
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt update
sudo apt install golang-go
```
Add /usr/local/go/bin & $HOME/go/bin directories to your $PATH:
```
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.profile
source $HOME/.profile
```
Or:
```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.1.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

Verify Go was installed correctly. Note that the fairyring binary requires Go v1.21:
```
go version
```
Setup validator name
Replace YOUR_MONIKER_GOES_HERE with your validator name

```
MONIKER="YOUR_MONIKER_GOES_HERE"
```
# Install dependencies
Update system and install build tools


Install Go


Download and build binaries

# Clone project repository
```
cd $HOME
rm -rf fairyring
git clone https://github.com/Fairblock/fairyring.git
cd fairyring
git checkout v0.5.0
```

# Build binaries
```
make install
```
Verify the installation:
```
fairyringd version
```
The output should show 0.5.0

# Prepare binaries for Cosmovisor
```
mkdir -p .fairyring/cosmovisor/genesis/bin
mv ~/go/bin/fairyringd .fairyring/cosmovisor/genesis/bin/
rm -rf build
```
Install Cosmovisor and create a service

# Download and install Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@lastest
```
Or:
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0
```
# Create service
```
sudo tee /etc/systemd/system/fairyringd.service > /dev/null << EOF
[Unit]
Description=osmosis node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
WorkingDirectory=.fairyring
Restart=always
RestartSec=5
LimitNOFILE=65535
Environment="DAEMON_HOME=.fairyring"
Environment="DAEMON_NAME=fairyringd"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.fairyring/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable fairyringd
```
Initialize the node

# Set node configuration
```
fairyringd config chain-id fairyring-testnet-1
fairyringd config node tcp://localhost:24757
```
# Initialize the node
```
fairyringd init $MONIKER --chain-id fairyring-testnet-1
```
# Download genesis and addrbook
```
curl -Ls https://raw.githubusercontent.com/Fairblock/fairyring/main/networks/testnets/fairyring-testnet-1/genesis.json > .fairyring/config/genesis.json
curl -Ls https://snapshots.lavenderfive.com/testnet-addrbooks/fairyring/addrbook.json > .fairyring/config/addrbook.json
```
# Add seeds
```
seeds="20e1000e88125698264454a884812746c2eb4807@seeds.lavenderfive.com:24756"
sed -i -e "s|^seeds *=.*|seeds = \"$seeds\"|" .fairyring/config/config.toml
```
# Set pruning
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  .fairyring/config/app.toml
```
# Set custom ports
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:24758\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:24757\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:24760\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:24756\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":24766\"%" .fairyring/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:24717\"%; s%^address = \":8080\"%address = \":24780\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:24790\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:24791\"%; s%:8545%:24745%; s%:8546%:24746%; s%:6065%:24765%" .fairyring/config/app.toml
```
Start service and check the logs
```
sudo systemctl start fairyringd && sudo journalctl -u fairyringd -f --no-hostname -o cat
```
Sync
To catch up quickly, you can either use a snapshot or state sync
