
# Side-Protocol (S2-testnet-2)

Update system
```
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```
Install Go
```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.22.1.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```
Install Node
```
cd $HOME
rm -rf sidechain && rm -rf side
git clone https://github.com/sideprotocol/side.git
```
```    
cd side
git checkout v0.8.1
```
```    
make install
 ```
```   
sided version
```
Initialize Node: Replace NodeName with your own moniker.
```
sided init NodeName
```
OR:
```
sided init NodeName --chain-id=S2-testnet-2
```
Here's your tutorial with the commands formatted for clarity:

1. Download the genesis file:
```sh
wget https://github.com/sideprotocol/testnet/raw/main/S2-testnet-2/genesis.json -O $HOME/.side/config/genesis.json
```

2. Verify the SHA256 hash of the downloaded genesis file:
```sh
shasum -a 256 $HOME/.side/config/genesis.json
```
Expected output:
```
2c6a6044de04cfdd466c9b114f0082af46c18519dc0ee413d2250de435b5d669  genesis.json
```

3. Set up seeds:
```sh
SEEDS="582dedd866dd77f25ac0575118cf32df1ee50f98@202.182.119.24:26656"
PEERS="582dedd866dd77f25ac0575118cf32df1ee50f98@202.182.119.24:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.side/config/config.toml
```
4. Set Pruning, Enable Prometheus, Gas Price, and Indexer
```
PRUNING="custom"
PRUNING_KEEP_RECENT="100"
PRUNING_INTERVAL="19"
sed -i -e "s/^pruning *=.*/pruning = \"$PRUNING\"/" $HOME/.side/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \
\"$PRUNING_KEEP_RECENT\"/" $HOME/.side/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \
\"$PRUNING_INTERVAL\"/" $HOME/.side/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.side/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.side/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.5uside\"/" $HOME/.side/config/app.toml
```

5. Start your node:
```sh
sided version
commit: 22e123457317f8edc949739c7310ea991b7a8100
cosmos_sdk_version: v0.47.9
go: go version go1.22.0 linux/amd64
name: sidechain
server_name: sided
version: 0.8.1
```
```sh
sided start
```

OR:
```sh
sudo systemctl restart sided
journalctl -u sided -f
```
Check syn: False
```sh
sided status 2>&1 | jq .SyncInfo.catching_up
```
# Validating

1. Add a **Bitcoin Segwit Address**
```sh
 sided keys add yournamewallet --key-type="segwit"
```
result must like this
```
- address: bc1q0xm60dd99hucpkux7rq6vr57g7k479nlw0xapt
  name: test
  pubkey: '{"@type":"/cosmos.crypto.segwit.PubKey","key":"A6gxg+M4sEu0MBFiYlj4r2fEaz/ueeaNE7ymf8Zx+Tqq"}'
  type: local
```

**Note:**
Please ensure that you use a Segwit address; otherwise, you will not be able to claim your rewards.

2. Create Validator
```sh
sided tx staking create-validator \
--from="test" \
--amount="10000000uside" \
--pubkey=$(sided tendermint show-validator)  \
--moniker="side_node" \
--security-contact="contact@side.one" \
--chain-id="S2-testnet-1" \
--commission-rate="0.05" \
--commission-max-rate="0.2" \
--commission-max-change-rate="0.05" \
--min-self-delegation="10000000" \
```
### Command
Create Service
```
sudo tee /etc/systemd/system/sided.service > /dev/null <<EOF
[Unit]
Description=sided Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which sided) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable sided
```

Query Wallet Balance
```
sided q bank balances $(sided keys show wallet -a)
```
 Create new Validator: Change your custom information at: Your_moniker, your_id_keybase, your_info. What items don't need to be able to be deleted
```
sided tx staking create-validator \
     --amount=1000000uside \
     --pubkey=$(sided tendermint show-validator) \
     --moniker="Your_moniker" \
     --identity=your_id_keybase  \
     --details="your_info" \
     --chain-id="S2-testnet-2" \
     --commission-rate="0.05" \
     --commission-max-rate="0.20" \
     --commission-max-change-rate="0.01" \
     --min-self-delegation="1" \
     --fees="200uside" \
     --from=wallet
```
 Edit Existing Validator:  Change your custom information.
```
sided tx staking edit-validator \
    --new-moniker="New_moniker" \
    --identity=new_identity \
    --details="new_info" \
    --website="your_website" \
    --chain-id=S2-testnet-1 \
    --from=wallet \
    --gas-prices=0.5uside \
    --gas-adjustment=1.5 \
    --gas=auto \
    -y
```
Delegate to your validator
```
sided tx staking delegate $(sided keys show wallet --bech val -a) 1000000uside --from wallet --chain-id side-testnet-3 --gas-prices 0.5uside --gas-adjustment 1.5 --gas auto -y
```
Unjail
```
sided tx slashing unjail --from wallet--chain-id side-testnet-2 --gas auto --fees 1000uside -y
```
Delete node
```
sudo systemctl stop sided && sudo systemctl disable sided && sudo rm /etc/systemd/system/sided.service && sudo systemctl daemon-reload && rm -rf $HOME/.side && rm -rf $HOME/.sidechain && rm -rf sidechain && rm -rf side && rm -rf rebus.core && sudo rm -rf $(which sided)
```
Send Token
```
sided tx bank send wallet <Receive_wallet> <Amout>uside --chain-id side-testnet-3 --gas-prices 0.5uside --gas-adjustment 1.5 --gas auto -y
```
                
            
    
    
