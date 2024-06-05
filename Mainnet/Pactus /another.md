# Pactus Blockchain
System Requirements: 
Ubuntu 22.04
Open port: 21888 TCP and UDP
Min: 1 CPU; 2Gb Ram; 20Gb SSD


`Important: Set tx_pool = 100 -> 50`

```
sed -i.bak '/^\s*[tx_pool]/, /^\s*[/{s/(max_size = )100/\150/;}' $HOME/pactus/config.toml
```



### Update Pactus version 1.1.8

**amd64 - x86**
```
cd $HOME && rm -rf node_pactus && wget https://github.com/pactus-project/pactus/releases/download/v1.1.8/pactus-cli_1.1.8_linux_amd64.tar.gz && tar -xzf pactus-cli_1.1.8_linux_amd64.tar.gz && rm -rf pactus-cli_1.1.8_linux_amd64.tar.gz && mv pactus-cli_1.1.8 node_pactus && cd node_pactus
```

**arm64 -m1**
```
cd $HOME && rm -rf node_pactus && wget https://github.com/pactus-project/pactus/releases/download/v1.1.8/pactus-cli_1.1.8_linux_arm64.tar.gz && tar -xzf pactus-cli_1.1.8_linux_arm64.tar.gz && rm -rf pactus-cli_1.1.8_linux_arm64.tar.gz && mv pactus-cli_1.1.8 node_pactus && cd node_pactus
```
### Server preparation:

```
sudo apt update && sudo apt upgrade -y && sudo apt install tmux git curl -y && sudo apt install make clang pkg-config libssl-dev build-essential -y 
```
Download Pactus v1.1.8: (amd64 - x86)
```
cd $HOME && rm -rf node_pactus && wget https://github.com/pactus-project/pactus/releases/download/v1.1.8/pactus-cli_1.1.8_linux_amd64.tar.gz && tar -xzf pactus-cli_1.1.8_linux_amd64.tar.gz && rm -rf pactus-cli_1.1.8_linux_amd64.tar.gz && mv pactus-cli_1.1.8 node_pactus && cd node_pactusDownload Pactus v1.1.6: (arm64 - M1)
```

Download Pactus v1.1.8: (arm64 - m1)
```
cd $HOME && rm -rf node_pactus && wget https://github.com/pactus-project/pactus/releases/download/v1.1.8/pactus-cli_1.1.8_linux_arm64.tar.gz && tar -xzf pactus-cli_1.1.8_linux_arm64.tar.gz && rm -rf pactus-cli_1.1.8_linux_arm64.tar.gz && mv pactus-cli_1.1.8 node_pactus && cd node_pactus
```

### Setup:

```
sudo ./pactus-daemon init
```
*Note: It is recommended to run 1 validator.
Save all important information in this step (Address validator, Wallet & seed).

If you already had a wallet before, you can use this command to restore the wallet
```
./pactus-daemon init --restore "<your-seed>"
```

Download snapshort: Once a day
```
sudo apt-get install unzip && cd $HOME/pactus && rm -rf data && wget https://node39.top/Mainnet/Pactus/pactus-data.zip && unzip pactus-data.zip
```

Start Pactus: Run in tmux
```
sudo ./pactus-daemon start
```

Wallet seed:
```
./pactus-wallet seed
```

List of Addresses:
```
./pactus-wallet address all
```

Check Balance:
```
./pactus-wallet address balance <ADDRESS>
```

Bond to the validator
```
./pactus-wallet tx bond <Reward address> <Validator address> <AMOUNT>
```

Send token
```
./pactus-wallet tx transfer <reward address> <receiver address> <AMOUNT>
```

Bootstrap node: (option)

Edit config:
```
nano $HOME/pactus/config.toml
```

Find [http]

```
// Edit

[http]
  enable = false -> true
  listen = "127.0.0.1:80" -> "127.0.0.1:8085"

// EX:
[http]
  enable = true
  listen = "127.0.0.1:8085"
Strat node: (Run in tmux)
```


```
sudo ./pactus-daemon start
```

Check node:
Save info Peer ID and Your ID
```
http://your_ip_node:8085/node
```
Fork: https://github.com/pactus-project/pactus

Pull requests: edit bootstrap.json

```
,
    {
        "name": "yourname",
        "email": "your_email",
        "website": "n/a",
        "address": "/ip4/your_ip/tcp/21888/p2p/your_peers_id"
    }
```
-> Pull request

Add a title: chore: add bootstrap address for <Name> (Change name)

-> DONE PR.

Edit Config:
```
nano $HOME/pactus/config.toml
```

Find [network] and [sync]
```
// Default
[network]
  network_key = "network_key"
  public_addr = ""
  listen_addrs = []
  bootstrap_addrs = []
  max_connections = 64
  enable_udp = false
  enable_nat_service = false
  enable_upnp = false
  enable_relay = true
  enable_relay_service = false
  enable_mdns = false
  enable_metrics = false
  force_private_network = false
[sync]
  moniker = "moniker"
  session_timeout = "10s"
  node_network = true
  
// Edit
[network]
  network_key = "network_key"
  public_addr = ""
  listen_addrs = []
  bootstrap_addrs = []
  max_connections = 64
  enable_udp = false
  enable_nat_service = true         #here
  enable_upnp = false
  enable_relay = true
  enable_relay_service = false
  enable_mdns = false
  enable_metrics = false
  force_private_network = false
[sync]
  moniker = "Node39."              #here
  session_timeout = "10s"
  node_network = true
```
Save: Ctrl + o and Ctrl + x
