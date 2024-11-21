![image](https://github.com/user-attachments/assets/c8313273-31af-498e-80c9-deae9aa16a76)

Recommended Hardware: 6 Cores, 16GB RAM, 400GB of storage (NVME), 100 Mb/s

## install dependencies, if needed
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```


## install go, if needed
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


OR (for user account)
```
[ ! -f $HOME/.bash_profile ] && touch $HOME/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
[ ! -d $HOME/go/bin ] && mkdir -p $HOME/go/bin
```

Check version
```
go version
```

## set vars
Port: `14` (you can change to another port)

```
echo "export MONIKER="Your-Monkier"" >> $HOME/.bash_profile
echo "export STORY_CHAIN_ID="odyssey-0"" >> $HOME/.bash_profile
echo "export STORY_PORT="14"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```


## download binaries
```
cd $HOME
wget -O geth https://github.com/piplabs/story-geth/releases/download/v0.10.1/geth-linux-amd64
chmod +x $HOME/geth
mv $HOME/geth ~/go/bin/
[ ! -d "$HOME/.story/story" ] && mkdir -p "$HOME/.story/story"
[ ! -d "$HOME/.story/geth" ] && mkdir -p "$HOME/.story/geth"
```


### install Story
```
cd $HOME
rm -rf story
git clone https://github.com/piplabs/story
cd story
git checkout v0.12.1
go build -o story ./client 
mv $HOME/story/story $HOME/go/bin/
```


## Cosmovisor Setup

### Install and init Cosmovisor:

```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
echo "export DAEMON_NAME="story"" >> $HOME/.bash_profile
echo "export DAEMON_HOME="$HOME/.story/story"" >> $HOME/.bash_profile
source $HOME/.bash_profile
cosmovisor init $(which story)
```

create directory:
```
cd $HOME
mkdir -p $HOME/.story/cosmovisor/genesis/bin
mkdir -p $HOME/.story/cosmovisor/upgrades
cp $HOME/go/bin/story $HOME/.story/cosmovisor/genesis/bin/
```

Check Story version
```
$HOME/go/bin/story version
```


## UPGRADE v0.12.1:
Create a directory and download the current version of story:

```
mkdir -p $HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin
wget -O $HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story https://github.com/piplabs/story/releases/download/v0.12.1/story-linux-amd64
chmod +x $HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story
```

### Check version:
```
$HOME/.story/story/cosmovisor/upgrades/v0.12.1/bin/story version
```

result
```
Version       v0.12.1-stable
Git Commit    20fed5e
Git Timestamp 2024-11-01T01:19:33Z
```

### Check upgrade info
```
cat $HOME/.story/story/cosmovisor/upgrades/v0.12.1/upgrade-info.json
```

result:
`{"name":"v0.12.1","time":"0001-01-01T00:00:00Z","height":322000}`


## UPGRADE v0.13.0: (prepare)
Create a directory and download the current version of story:

```
mkdir -p $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin
wget -O $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story https://github.com/piplabs/story/releases/download/v0.13.0/story-linux-amd64
chmod +x $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story
```

### Check version:
```
$HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story version
```

result
```
Version       v0.13.0-stable
Git Commit    daaa395
Git Timestamp 2024-11-19T03:59:16Z
```

### Create upgrade simlink:

```
echo '{"name":"v0.13.0","time":"0001-01-01T00:00:00Z","height":858000}' > $HOME/.story/story/cosmovisor/upgrades/v0.13.0/upgrade-info.json
```

### Check current simlink:
```
ls -l $HOME/.story/story/cosmovisor/current
```
result: `lrwxrwxrwx 1 story story 16 Nov 21 09:26 /home/story/.story/story/cosmovisor/current -> upgrades/v0.12.1`


### Check upgrade info:
```
cat $HOME/.story/story/cosmovisor/upgrades/v0.13.0/upgrade-info.json
```

result: `{"name":"v0.13.0","time":"0001-01-01T00:00:00Z","height":858000}`


### Sets up an automatic upgrade in Cosmovisor:
```
cosmovisor add-upgrade v0.13.0 $HOME/.story/story/cosmovisor/upgrades/v0.13.0/bin/story --force --upgrade-height 858000
```

result:
```
10:13AM INF Using /home/story/.story/story/cosmovisor/upgrades/v0.13.0/bin/story for v0.13.0 upgrade module=cosmovisor
10:13AM INF Upgrade binary located at /home/story/.story/story/cosmovisor/upgrades/v0.13.0/bin/story module=cosmovisor
10:13AM INF /home/story/.story/story/data/upgrade-info.json created, v0.13.0 upgrade binary will switch at height 858000 module=cosmovisor
```



## Create service file:
```
sudo tee /etc/systemd/system/story.service > /dev/null << EOF
[Unit]
Description=story node service
After=network-online.target

[Service]
User=$USER
Environment="DAEMON_NAME=story"
Environment="DAEMON_HOME=$HOME/.story/story"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_DATA_BACKUP_DIR=$HOME/.story/story/data"
ExecStart=$(which cosmovisor) run run
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```





## init story app
```
story init --moniker $MONIKER --network odyssey
```



## set seeds and peers
```
SEEDS="434af9dae402ab9f1c8a8fc15eae2d68b5be3387@story-testnet-seed.itrocket.net:29656"
PEERS="c2a6cc9b3fa468624b2683b54790eb339db45cbf@story-testnet-peer.itrocket.net:26656,a8a2e0e0d5875741419a8c5a73b41ba265e1e895@37.27.69.177:26656,8b241f57d1375205aa4a17d038f9825a516ccbc5@88.99.252.213:36656,d9e7ded2f67bdca023e67999284d7d3664daf700@149.50.108.172:26656,34400e930af9ff63a0c2c2d1b036a8763e7c92e1@158.220.126.24:52656,d39a6b6b4e6ca9bd8b49cf4567bf89fafe1218c3@100.42.183.151:656,11dc2be345d15741dc747d6a6e5bcdc90d812756@100.42.183.223:656,16e5119da2cd37c561b65f645a202755518f8a6a@88.198.27.51:52656,eddcbbcce52cb6725a82aff1fa6de66e991fdeca@38.242.128.250:52656,acbb5e639d2985f09886fc7da6c247d024ee54fc@100.42.183.224:656,54a2735f0a96a3a6eec089652c1aa8f899d5769a@195.7.5.165:26656,e96d4dfe2871aa44a5d97bca9ac585ad16647503@185.255.131.5:52656,ed27db8cbb711f8d3e7f204e39405f8b19531889@46.4.52.158:26656,7f811d3160086e1cdfe1ea01823f7b90df98a7ab@37.60.234.139:656,26cac71cbc88d50d08d363aa889c6a52e07973d0@100.42.183.131:656,c2584a9a1c75e5c0f3c46a08ee58c5dab169a212@62.169.27.220:26656,29b153902e6e03a7254e3afebdcd6d6c5802ab00@100.42.183.183:656,9e2fabda41e3c3317c25f5ef6c604c1d78370aba@50.112.252.101:26656,aae0f9caab7cd55bd9c7f5e8df5ad545eb7e56da@100.42.183.50:656,ec9b94601137488dd8631a26db501d123f35ffba@158.220.127.86:656,16b98ab14c106c6dbb26d21beb6b77b6e611fe32@135.181.212.253:2010,f6598752cd85337d4c3a39980fefd6f2d6390668@198.7.113.49:656,d9669dafd21c552e06c82780803f72cdfae22193@158.220.120.148:26656,396710d357b98220bc5f9c4d11c56392db631c30@161.97.174.80:21656,13c8b01ac0a0794a5ffbc691b6c6e51644570964@212.47.70.111:26656,75ac7b193e93e928d6c83c273397517cb60603c0@3.142.16.95:26656,69950ba769347d99938a171da6084ea7985a09ed@37.60.236.169:656,cd33da43885ce09266d5019d996f2094a5521b85@81.0.246.245:656,c946e71d9fe86548e1ea38c9a1affef683d5ff2c@158.220.119.126:26656,3570054e30c388aa4c3d392cc9962846302ca11c@45.149.207.111:656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.story/story/config/config.toml
```


## download genesis and addrbook
```
wget -O $HOME/.story/story/config/genesis.json https://server-3.itrocket.net/testnet/story/genesis.json
wget -O $HOME/.story/story/config/addrbook.json  https://server-3.itrocket.net/testnet/story/addrbook.json
```


## set custom ports in story.toml file
```
sed -i.bak -e "s%:1317%:${STORY_PORT}317%g;
s%:8551%:${STORY_PORT}551%g" $HOME/.story/story/config/story.toml
```

## set custom ports in config.toml file
```
sed -i.bak -e "s%:26658%:${STORY_PORT}658%g;
s%:26657%:${STORY_PORT}657%g;
s%:26656%:${STORY_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${STORY_PORT}656\"%;
s%:26660%:${STORY_PORT}660%g" $HOME/.story/story/config/config.toml
```

## enable prometheus and disable indexing
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.story/story/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.story/story/config/config.toml
```


## create geth servie file
```
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/go/bin/geth --odyssey --syncmode full --http --http.api eth,net,web3,engine --http.vhosts '*' --http.addr 0.0.0.0 --http.port ${STORY_PORT}545 --authrpc.port ${STORY_PORT}551 --ws --ws.api eth,web3,net,txpool --ws.addr 0.0.0.0 --ws.port ${STORY_PORT}546
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```





## download snapshots
### backup priv_validator_state.json
```
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup
```

### remove old data and unpack Story snapshot

```
rm -rf $HOME/.story/story/data
curl https://server-3.itrocket.net/testnet/story/story_2024-11-21_713429_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/story
```


### restore priv_validator_state.json
```
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
```

### delete geth data and unpack Geth snapshot
```
rm -rf $HOME/.story/geth/odyssey/geth/chaindata
mkdir -p $HOME/.story/geth/odyssey/geth
curl https://server-3.itrocket.net/testnet/story/geth_story_2024-11-21_713429_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/geth/odyssey/geth
```


### enable and start geth, story
```
sudo systemctl daemon-reload
sudo systemctl enable story story-geth
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
```


### check logs (in new Tmux)
```
journalctl -u story -u story-geth -f
```


### Check sync status:
```
curl localhost:${STORY_PORT}657/status | jq
```

```
curl -s localhost:${STORY_PORT}657/status | jq .result.sync_info
```


```
curl -s localhost:${STORY_PORT}657/status | jq .result.sync_info.catching_up
```


# END










