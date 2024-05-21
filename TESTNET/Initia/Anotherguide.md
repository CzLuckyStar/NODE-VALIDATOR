# INITIA NODE, VALIDATOR & ORACLE
Testnet
### The minimum hardware requirements for running an Initia node are:
  ```
  CPU: 4 cores
  Memory: 16GB RAM
  Disk: 1 TB SSD Storage
  Bandwidth: 100 Mbps
  ```

## Install go and all necessary tools:
```
wget https://go.dev/dl/go1.22.3.linux-amd64.tar.gz && \
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.22.3.linux-amd64.tar.gz && \
export PATH=$PATH:/usr/local/go/bin && \
echo "export PATH=$PATH:/usr/local/go/bin" >> $HOME/.profile && \
rm go1.22.3.linux-amd64.tar.gz && \
source $HOME/.profile && \
go env GOBIN='/usr/local/go/bin/' && \
sudo apt install -y build-essential jq
```
Check go:
```
go version
```
Download initia and install:
```
cd "$HOME" && git clone https://github.com/initia-labs/initia && \
cd initia && git checkout v0.2.15 && make install && \
ln -s /usr/local/go/bin/initiad /usr/bin/
```
Check initiad:
```
initiad version
```
Add peers:
```
PEERS=$(curl -s --max-time 3 --retry 2 --retry-connrefused "https://rpc-initia-testnet.trusted-point.com/hetzner_peers.txt") \
if [ -z "$PEERS" ]; then \
    echo "No peers were retrieved from the URL." \
else \
    echo -e "\nPEERS: "$PEERS"" \
    sed -i "s/^persistent_peers =./persistent_peers = "$PEERS"/" "$HOME/.initia/config/config.toml" \
    echo -e "\nConfiguration file updated successfully.\n" \
fi
```
Add seeds:
```
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756,cd69bcb00a6ecc1ba2b4a3465de4d4dd3e0a3db1@initia-testnet-seed.itrocket.net:51656" && \
sed -i -e 's/^seeds *=.*|seeds = "${SEEDS}"|' $HOME/.initia/config/config.toml
```
(OPTIONAL) Change ports:
```
CUSTOM_PORT=48 && \
sed -i.bak -e "s%:26658%:${CUSTOM_PORT}658%g;
s%:26657%:${CUSTOM_PORT}657%g;
s%:6060%:${CUSTOM_PORT}060%g;
s%:26656%:${CUSTOM_PORT}656%g;
s%:26660%:${CUSTOM_PORT}660%g" $HOME/.initia/config/config.toml && \
sed -i.bak -e "s%:1317%:${CUSTOM_PORT}317%g;
s%:8080%:${CUSTOM_PORT}080%g;
s%:9090%:${CUSTOM_PORT}090%g;
s%:9091%:${CUSTOM_PORT}091%g;
s%:8545%:${CUSTOM_PORT}545%g;
s%:8546%:${CUSTOM_PORT}546%g;
s%:6065%:${CUSTOM_PORT}065%g" $HOME/.initia/config/config.toml
```
Dissable the indexer:
```
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.initia/config/config.toml
```
Set Minimum Gas Prices:
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml
```
Update ulimit:
```
ulimit -n 65535 && \
echo -e "*                soft    nofile          65535\n\
*                hard    nofile          65535" >> /etc/security/limits.conf
```
Set IP: (If you are running in your own PC this command doesn’t work, you must use your public IP)
```
IP=$(hostname -I | awk '{print $1}') && \
sed -i -e 's|^external_address *=.*|external_address = "${IP}:26656"|' $HOME/.initia/config/config.toml
```
If you set a custom port:
```
IP=$(hostname -I | awk '{print $1}') && \
sed -i -e 's|^external_address *=.*|external_address = "${IP}:${CUSTOM_PORT}656"|' $HOME/.initia/config/config.toml
```
Init:
```
initiad init <moniker> --chain-id initiation-1
```
Use initiad at service:
```
tee /etc/systemd/system/initiad.service > /dev/null <<EOF
[Unit]
Description=initiad
[Service]
Type=simple
User=$USER
ExecStart=$(which initiad) start
Restart=on-abort
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=initiad
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target 
EOF
```
Download a Snapshot: This takes some minutes
```
sudo apt update && \
sudo apt install snapd -y && \
sudo snap install lz4 && \
cp ~/.initia/data/priv_validator_state.json  ~/.initia/priv_validator_state.json && \
initiad tendermint unsafe-reset-all --keep-addr-book --home $HOME/.initia && \
curl -o - -L https://snapshots.polkachu.com/testnet-snapshots/initia/initia_218063.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.initia && \
mv ~/.initia/priv_validator_state.json  ~/.initia/data/priv_validator_state.json
```
Launch:
```
sudo systemctl daemon-reload && \
sudo systemctl enable initiad && \
sudo systemctl restart initiad && \
sudo journalctl -u initiad -f -o cat
```
## Validator
Create your validator when your node is synced
Check node height:
```
local_height=$(initiad status | jq -r .sync_info.latest_block_height); network_height=$(curl -s https://rpc-initia-testnet.trusted-point.com/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```
Add new key:
```
initiad keys add wallet
```
Show your wallet address:
```
initiad keys show wallet -a
```
Request tokens in the faucet: https://faucet.testnet.initia.xyz/
Check balance:
```
initiad query bank balances $(initiad keys show wallet -a)
```
Create validator:
```
initiad tx mstaking create-validator \
--amount 1000000uinit \
--pubkey $(initiad tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```
Congrats! your validator is done!
Update validator info:
```
initiad tx mstaking edit-validator \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```
Delegate yourself:
```
initiad tx mstaking delegate $(initiad keys show wallet --bech val -a) 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```
Unjail your validator:
```
initiad tx slashing unjail --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```
## Oracle
Download and install:
```
cd "$HOME" && git clone https://github.com/skip-mev/slinky.git && cd slinky && \
git checkout v0.4.3 && make build && \
ln -s "$HOME/slinky/build/slinky" "/usr/bin/"
```
Update app.toml:
```
sed -i '0,/^enabled *=/{//!b};:a;n;/^enabled *=/!ba;s|^enabled *=.*|enabled = "true"|' $HOME/.initia/config/app.toml && \
sed -i -e 's|^oracle_address *=.*|oracle_address = "127.0.0.1:8080"|' $HOME/.initia/config/app.toml && \
sed -i -e 's|^client_timeout *=.*|client_timeout = "300ms"|' $HOME/.initia/config/app.toml
```
Use Oracle at service:
```
tee /etc/systemd/system/oracle.service > /dev/null <<EOF
[Unit]
Description=oracle

[Service]
Type=simple
User=$USER
ExecStart=/usr/bin/slinky --oracle-config-path "$HOME/slinky/config/core/oracle.json" --market-map-endpoint 0.0.0.0:9090
Restart=on-abort
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=oracle
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
Launch oracle and restart node:
```
sudo systemctl daemon-reload && \
sudo systemctl enable oracle && \
sudo systemctl restart oracle && \
sudo systemctl restart initiad && \
sudo journalctl -u oracle -f -o cat
```
