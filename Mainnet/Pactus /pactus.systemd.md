# PACTUS SystemD
Pactus is a decentralized blockchain platform, emphasizing fairness and transparency without the need for delegation or miners, enabling anyone to join and maintaining true decentralization.
[Pactus](https://pactus.org/)

## Recommended Hardware Requirements 

|   SPEC      |        Recommend          |
| :---------: | :-----------------------: |
|   **CPU**   |        2 Cores            |
|   **RAM**   |        4GB  Ram           |
|   **SSD**   |        64GB SSD           | 

# 1. Update system and install build tools
```
sudo apt update && sudo apt list --upgradable && sudo apt upgrade -y
```
# 2. Additional package:
```
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential git make ncdu net-tools -y
```
# 3. Install go
```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.3.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
# 4. Install binary & build binary
```
cd $Home
git clone https://github.com/pactus-project/pactus.git .pactus
cd .pactus
make build
cd build
```
For new wallet
```
./pactus-daemon init
```
For recover wallet
```
./pactus-daemon init --restore "Your seed phrase"
```
# 5. Create & start service
```
sudo tee /etc/systemd/system/pactusd.service > /dev/null << EOF
[Unit]
Description=pactus Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=root
ExecStart= /root/.pactus/build/pactus-daemon start -w /root/pactus --password "Your pass"
Restart=always
RestartSec=120
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable pactusd
```
```
sudo systemctl restart pactusd && journalctl -f -u pactusd
```
# 6. Change Config.toml
```
nano $HOME/pactus/config.toml
```
### Before
> [http]
> 
> enable = false
> 
> listen = "127.0.0.1:80"
> 
### After
> [http]
> 
> enable = true
> 
> listen = "0.0.0.0:80"
> 
```
sudo systemctl stop pactusd
sudo systemctl restart pactusd
```
### Check node ID.
http://***your_ip_node***:80/node
# 7. Update bootstrap. Create a new Fork and pull request to Pactus Github
[Link github](https://github.com/pactus-project/pactus)

