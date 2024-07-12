
# Fairblock

Fairblock is revolutionizing blockchain networks by addressing the critical need for encryption, a barrier to global adoption. By offering programmable encryption solutions, Fairblock integrates advanced cryptographic schemes like threshold identity-based encryption (tIBE) and threshold fully homomorphic encryption (tFHE) into both application frontends and protocol levels.

This approach provides robust protection for transactions, shielding them from malicious bots and competitors. Fairblock’s solutions are versatile, supporting use cases such as encrypted on-chain trading, private governance, and censorship-resistant sequencing.

Fairblock's innovations unlock new possibilities for secure, compliant, and efficient decentralized applications across all blockchain networks, not just privacy-specific layers. This makes Fairblock a pivotal player in enhancing on-chain experiences and fostering trust in blockchain ecosystems.

Website:

GitHub:

Twitter:

Discord:

Docs:

Available Explorers:

Website:

Public    Endpoints

`RPC:`    `https://fairblock-testnet-rpc.crouton.digital`
`API:`    `https://fairblock-testnet-api.crouton.digital`
`gRPC:`   `fairblock-testnet-api.crouton.digital:28890`
`peer:`   `a3a5f46aae6af829e5070f7182c3d178a091a64d@65.109.93.124:28856`

## Prepare

### Update and install packages

This command updates and installs various packages needed for development and system administration. It includes tools for networking, archiving, compiling, version control, and other essential utilities.
```
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop lz4 -y
```

## Details 📚

### Installing Go v1.21.6

This command downloads and installs go version go1.21.6 linux/amd64. It sets up the Go environment by updating the PATH variable and verifies the installation.



GO_VERSION: `1.21.6`
```
cd $HOME && \ 
wget "https://golang.org/dl/go1.21.6.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go1.21.6.linux-amd64.tar.gz" && \
rm "go1.21.6.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## Installing fairblock binary

This command installs the fairblock binary, offering two methods: building from source or downloading a prebuilt binary.

### Building from Source

```
git clone https://github.com/Fairblock/fairyring.git fairblock 
cd fairblock 
go mod tidy
git checkout v0.6.0 
make install
fairyringd version --long | grep -e version -e commit
# version: v0.6.0
# commit: 248c5546b299f2c79147c52f2aaf2578f32bc651
```

OR:

### Download Prebuilt Binary

```
wget https://storage.crouton.digital/testnet/fairblock/bin/fairyringd
chmod +x fairyringd
mkdir -p $HOME/go/bin
mv fairyringd $HOME/go/bin
fairyringd version --long | grep -e version -e commit
# version: v0.6.0
# commit: 248c5546b299f2c79147c52f2aaf2578f32bc651
```


### Initialization
This command initializes the node with a given moniker (node name) and chain ID, then sets the client configuration to use the specified chain ID and a test keyring backend.


MONIKER: `VALIDATOR_NAME`

```
fairyringd init VALIDATOR_NAME --chain-id fairyring-testnet-1 && \
fairyringd config chain-id fairyring-testnet-1 && \
fairyringd config keyring-backend test
```


### Download genesis

The genesis file is essential for starting the blockchain. It contains the initial state of the blockchain and is typically downloaded from the official hosting site or a trusted mirror. Our genesis file is uploaded from a fully-synced node, but it is always recommended to verify its contents.
```
wget https://storage.crouton.digital/testnet/fairblock/files/genesis.json -O $HOME/.fairyring/config/genesis.json
```

### Download addrbook

An addrbook.json file helps your node connect to other peers in the network. If you experience peering issues, using a good addrbook.json can solve the problem.
```
wget https://storage.crouton.digital/testnet/fairblock/files/addrbook.json -O $HOME/.fairyring/config/addrbook.json
```

### Configuration

Change ports (Optional)

This script changes the ports used by various services in the config.toml and app.toml files based on your node number. It also sets an environment variable for the node's local address. This step is optional and can help avoid port conflicts.


NODE-NUMBER: `1`

```
EXTERNAL_IP=$(wget -qO- eth0.me)

sed -i.bak \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((1 + 266))58\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((1 + 266))57\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$((1 + 60))60\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((1 + 266))56\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$((1 + 266))56\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$((1 + 266))56\"/}" \
    -e "s/\(prometheus_listen_addr = \":\)\([0-9]*\).*/\1$((1 + 266))60\"/"                            $HOME/.fairyring/config/config.toml

sed -i.bak \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 13))17\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 90))90\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 90))91\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 85))45\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(ws-address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((1 + 85))46\4/}"  $HOME/.fairyring/config/app.toml 
    
echo "export NODE=http://localhost:$((1+266))57" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
fairyringd config node $NODE
```

### Web Services (Optional)

This optional step allows you to configure web services for your node. You can open the RPC address, enable or disable API, gRPC, gRPC-Web, and JSON-RPC services, and set the minimum gas prices.

RPC-OPEN: `127.0.0.1`

API-ENABLE: `false`

gRPC-ENABLE:  `false`

gRPC-WEB-ENABLE:  `false`

JSON-RPC-ENABLE:  `false`

GAS-PRICE: `0.001`

```
sed -i.bak -e "/^\[rpc\]/,/^laddr =/s|laddr = \"tcp://127\.0\.0\.1:|laddr = \"tcp://127.0.0.1:|"        $HOME/.fairyring/config/config.toml
sed -i.bak -e "/^\[api\]/,/^enable/s|^enable *=.*|enable = 'false'|"                                    $HOME/.fairyring/config/app.toml
sed -i.bak -e "/^\[grpc\]/,/^enable/s|^enable *=.*|enable = 'false'|"                                   $HOME/.fairyring/config/app.toml
sed -i.bak -e "/^\[grpc-web\]/,/^enable/s|^enable *=.*|enable = 'false'|"                               $HOME/.fairyring/config/app.toml
sed -i.bak -e "/^\[json-rpc\]/,/^enable/s|^enable *=.*|enable = 'false'|"                               $HOME/.fairyring/config/app.toml
sed -i.bak -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = '0.001ufairy'|"                          $HOME/.fairyring/config/app.toml
```

### Pruning (Optional)

This optional step allows you to configure pruning settings for your node. You can enable or disable the indexer, set custom pruning options, and define intervals for pruning and snapshots to optimize disk space usage.



INDEXER-ENABLE: `null`

PRUNING: `custom`

PRUNING-KEEP-RECENT: `100`

PRUNING-KEEP-EVERY: `0`

PRUNING-INTERVAL: `17`

SNAPSHOT-INTERVAL: `100`

```
sed -i.bak -e "s/^indexer *=.*/indexer = \"null\"/"                                                     $HOME/.fairyring/config/config.toml
sed -i.bak -e "s/^pruning *=.*/pruning = \"custom\"/"                                                   $HOME/.fairyring/config/app.toml
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/"                              $HOME/.fairyring/config/app.toml
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"0\"/"                                  $HOME/.fairyring/config/app.toml 
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"17\"/"                                     $HOME/.fairyring/config/app.toml
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"100\"/"                                  $HOME/.fairyring/config/app.toml
```

### Create a service file

Create a service file to manage the node with systemd.

```
sudo tee /etc/systemd/system/fairyringd.service > /dev/null <<EOF
[Unit]
Description=fairblock_node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which fairyringd) start --home $HOME/.fairyring
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

```
Start the node
Start the node service and enable it to run on startup.
```

```
sudo systemctl daemon-reload && \
sudo systemctl enable fairyringd && \
sudo systemctl restart fairyringd && \
sudo journalctl -u fairyringd -f -o cat
```

This will create a systemd service for your node, enable it to start on boot, and start it immediately. You can monitor the node's logs using `journalctl`.

### Recover

These steps will help you recover your node either by downloading a snapshot.

```
sudo systemctl stop fairyringd
cp $HOME/.fairyring/data/priv_validator_state.json $HOME/.fairyring/priv_validator_state.json.backup
rm -rf $HOME/.fairyring//data 
curl https://storage.crouton.digital/testnet/fairblock/snapshots/fairblock_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.fairyring
mv $HOME/.fairyring/priv_validator_state.json.backup $HOME/.fairyring/data/priv_validator_state.json
sudo systemctl restart fairyringd && sudo journalctl -u fairyringd -f
```

### Upgrade Node



APP_VERSION: `v0.6.0`
```
cd fairblock 
git pull
git checkout tags/v0.6.0 -b v0.6.0
make install
sudo systemctl restart fairyringd && sudo journalctl -u fairyringd -f
```

### Delete Node
```
sudo systemctl stop fairyringd
sudo systemctl disable fairyringd
sudo rm -rf /etc/systemd/system/fairyringd.service
sudo rm $(which fairyringd)
sudo rm -rf $HOME/.fairyring
```

## Key management


WALLET: `wallet`

### Add New Wallet

This command generates a new wallet with a unique keypair.

Use this to create a secure, brand-new wallet for transactions.
```
fairyringd keys add wallet 
```

### Recover Wallet
```
# This command restores a wallet using a mnemonic phrase.
# Ideal for accessing a previously created wallet on a new device.
fairyringd keys add wallet --recover
```

### Delete Wallet
```
# This command removes a wallet from the node's key management system.
# Ensure you have backed up the key before deleting, as this action is irreversible.
fairyringd keys delete wallet
``` 


### List All Wallets
```
# This command lists all the wallets managed by the node.
# Useful for getting an overview of all available wallets and their statuses.
fairyringd keys list
``` 


### Export Key
```
# This command exports the wallet's private key to a file.
# Use this to backup your wallet's key securely.
fairyringd keys export wallet > wallet.backup
```

### Import Key
```
# This command imports a wallet key from a backup file.
# Essential for restoring wallet access from a previously exported key file.
fairyringd keys import wallet  wallet.backup
```

## Validator management


MONIKER: `VALIDATOR_NAME`
 
IDENTITY: 
 
WEBSITE: 
 
DETAILS: 

WALLET: `wallet`

COMISSION-RATE: `0.1`

MAX-RATE: `0.2`

CHANGE-RATE: `0.01`


### Create Validator
```
fairyringd tx staking create-validator \
--amount 1000000ufairy \
--moniker "VALIDATOR_NAME" \
--identity "" \
--website "" \
--details "" \
--from wallet \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--pubkey $(fairyringd tendermint show-validator) \
--min-self-delegation 1 \
--chain-id fairyring-testnet-1 \
--gas 2000000 \
--fees 5500ufairy \
-y 
```

### Edit Validator
```
fairyringd tx staking edit-validator \
--moniker "VALIDATOR_NAME" \
--identity "" \
--website "" \
--details "" \
--from wallet \
--commission-rate 0.1 \
--chain-id fairyring-testnet-1 \
--gas 2000000 \
--fees 5500ufairy \
-y 
```


WALLET: `wallet`

### Validator Details
```
# Retrieves comprehensive information regarding the validator.
# This command is utilized to fetch details about the current validator, including its address, public key, and associated data.
fairyringd q staking validator $(fairyringd keys show wallet --bech val -a) 
```


### Signing Info
```
# Fetches information about the validator's signing behavior.
# This command extracts details concerning the signing behavior associated with the current validator, including missed block counts and other security-related parameters.
fairyringd q slashing signing-info $(fairyringd tendermint show-validator) 
``` 

### Slashing Parameters
```
# Retrieves parameters pertinent to slashing.
# This command extracts the current parameters governing slashing, such as threshold values for penalties and punishment durations.
fairyringd q slashing params 
``` 

### Check Key
```
# Validates the status of your key.
# This command compares the public key of your validator with the key associated with your node to ensure that the key is in an up-to-date state.
[[ $(fairyringd q staking validator $(fairyringd keys show wallet  --bech val -a) -oj | jq -r .consensus_pubkey.key) == $(fairyringd status | jq -r .ValidatorInfo.PubKey.value) ]]
echo -e "Your key status is ok" || echo -e "Your key status is error"
``` 

### Jailing Info
```
# Retrieves information about validator penalties.
# This command fetches details about current penalties imposed on the validator, including information about blocks where signatures were missed.
fairyringd q slashing signing-info $(fairyringd tendermint show-validator) 
```

### Active Set
```
# Obtains details of the active set of validators.
# This command retrieves information about all validators currently in the active set, their current statuses, and other associated parameters.
fairyringd q staking validators -o json --limit=1000 \
| jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' \
| jq -r '.tokens + " - " + .description.moniker' \
| sort -gr | nl
``` 

### Inactive Set
```
# Obtains details of the inactive set of validators.
# This command fetches information about all validators currently in the inactive set, their current statuses, and other associated parameters.
fairyringd q staking validators -o json --limit=1000 \
| jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' \
| jq -r '.tokens + " - " + .description.moniker' \
| sort -gr | nl
``` 


 
## Onchain


WALLET: `wallet`

### Withdraw Rewards
```
# Withdraw all accumulated rewards from your account.
# This command retrieves and withdraws all rewards earned from staking.
fairyringd tx distribution withdraw-all-rewards --from wallet --chain-id fairyring-testnet-1 --gas-adjustment=1.15 --gas auto --gas-prices 80000ufairy -y
```

### Withdraw Rewards & Commission
```
# Withdraw rewards and commission earned from your validator.
# Use this command to claim both validator rewards and commission earnings.
fairyringd tx distribution withdraw-rewards  --from wallet --commission --chain-id fairyring-testnet-1 --gas-adjustment=1.15 --gas auto 80000ufairy -y 
```

### Check Balance
```
# Check the balance of your account.
# This command displays the current balance of your account.
fairyringd query bank balances $WALLET_ADDRESS
``` 

### Delegate to Self
```
# Delegate tokens from your account to yourself as a validator.
# This command allows you to stake tokens on your own validator node.
fairyringd tx staking delegate $(fairyringd keys show wallet --bech val -a) 1000000ufairy --from wallet --chain-id fairyring-testnet-1 --gas-adjustment=1.15 --gas auto --gas-prices 80000ufairy -y 
``` 

### Delegate to Other
```
# Delegate tokens to another validator.
# Use this command to stake tokens on a validator other than yourself.
 
```

### Redelegate Stake
```
# Redelegate tokens from one validator to another.
# This command allows you to switch your stake from one validator to another.
 
```

### Unbond Tokens
```
# Unbond tokens from staking.
# Use this command to initiate the unbonding process for your staked tokens.
 
```

### Transfer Funds
```
# Transfer funds to another account.
# This command allows you to send tokens to another account on the network.
 
```

# END

 
