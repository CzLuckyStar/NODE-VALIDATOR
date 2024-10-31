
# Roll back: 
(20241031) 

Hello again Validators!

We have now identified the issue causing the AppHash errors during the last few days!
The issue was caused by bad combination of mempool configurations that are not compatible with EVMOS.
This then caused some validators to have different AppHashes for blocks.

We have made a new release v0.5.3 that forces the NoOp mempool configuration for the node.

These are the steps to fix your node:
- Stop your node
- Update to binary v0.5.3
- Do a 
```
wardend rollback --hard
```
- Start your node
******************************************************************************************************************************
# & Use Cosmovisor
(KHONG CAN `rollback` vi khong bi apphash)
```
sudo systemctl stop wardend
```

```
cd $HOME
rm -rf bin
mkdir bin && cd bin
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.5.3/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip
chmod +x wardend
sudo mv $HOME/bin/wardend $(which wardend)
```

```
cd $HOME
mkdir -p $HOME/.warden/cosmovisor/genesis/bin
mkdir -p $HOME/.warden/cosmovisor/upgrades
cp $HOME/go/bin/wardend $HOME/.warden/cosmovisor/genesis/bin/
```

Check version:
```
$HOME/go/bin/wardend version --long | tail
```
or
```
$HOME/.warden/cosmovisor/genesis/bin/wardend version --long | tail
```

result:
```
- pgregory.net/rapid@v1.1.0
- sigs.k8s.io/yaml@v1.4.0
build_tags: ""
commit: 197a6f653d6d318001a9c89d5466d0ca36e03a79
cosmos_sdk_version: v0.50.9-evmos-warden
go: go version go1.22.5 linux/amd64
name: warden
server_name: wardend
version: 0.5.3
```

Download and install Cosmovisor
```
cd $HOME
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

Create service:
```
sudo tee /etc/systemd/system/wardend.service > /dev/null << EOF
[Unit]
Description=Cosmovisor daemon
After=network-online.target

[Service]
Environment="DAEMON_NAME=wardend"
Environment="DAEMON_HOME=$HOME/.warden"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_POLL_INTERVAL=300ms"
Environment="DAEMON_DATA_BACKUP_DIR=$HOME/.warden"
Environment="UNSAFE_SKIP_BACKUP=false"
Environment="DAEMON_PREUPGRADE_MAX_RETRIES=0"
User=$USER
ExecStart=$HOME/go/bin/cosmovisor run start
Restart=on-failure
RestartSec=10
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```


```
sudo systemctl daemon-reload
sudo systemctl enable wardend
sudo systemctl restart wardend
```



