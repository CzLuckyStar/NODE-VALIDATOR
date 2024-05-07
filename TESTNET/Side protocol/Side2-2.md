# S2-testnet-2

## Cloning SIDE Repository and Setup
1. Clone the SIDE repository:
   ```sh
   git clone https://github.com/sideprotocol/side.git
   ```

2. Checkout to the desired version:
   ```sh
   cd side 
   git checkout v0.8.1
   ```

3. Install SIDE:
   ```sh
   make install
   sided version
   ```

# Launch
Here's your tutorial with the commands formatted for clarity:

1. Download the genesis file:
```sh
wget https://github.com/sideprotocol/testnet/raw/main/S2-testnet-2/genesis.json -O ~/.side/config/genesis.json
```

2. Verify the SHA256 hash of the downloaded genesis file:
```sh
shasum -a 256 ~/.side/config/genesis.json
```
Expected output:
```
2c6a6044de04cfdd466c9b114f0082af46c18519dc0ee413d2250de435b5d669  genesis.json
```

3. Intitialize the node
```sh
  sided init LuckyStar --chain-id=S2-testnet-2
```
    
4. Set up seeds:
```sh
   PEERS=""
   SEEDS="582dedd866dd77f25ac0575118cf32df1ee50f98@202.182.119.24:26656" && \
   sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.side/config/config.toml
```

5. Create Service:
```sh
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

6. Start your node:
```sh
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

```sh
journalctl -u sided -f
```

Check syn: False
```sh
sided status 2>&1 | jq .SyncInfo.catching_up
```

# Validating

1. Add a **Bitcoin Segwit Address**
```sh
 sided keys add test --key-type="segwit"

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
--chain-id="S2-testnet-2" \
--commission-rate="0.1" \
--commission-max-rate="0.2" \
--commission-max-change-rate="0.05" \
--min-self-delegation="10000000" \
--gas auto \
--gas-adjustment 1.5 \
--fees 500uside \
--y
```
