# Installation for VPS

## Install Update
```
sudo apt -q update
sudo apt -qy upgrade
```

## Installing Podman and Wireguard
```
sudo apt-get -y install podman
sudo apt install wireguard
```

 
## Download binaries
```
curl -skSL --retry 3 https://storage.googleapis.com/sonaric-releases/stable/linux/sonaric-amd64-latest.tar.gz | sudo tar -xz -C /usr/bin
```

## Create service file
```
sudo tee /etc/systemd/system/sonaricd.service > /dev/null <<EOF
[Unit]
Description=sonaric node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which sonaricd) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

 ```
sudo systemctl daemon-reload
sudo systemctl start sonaricd && sudo journalctl -fu sonaricd -o cat
```

 # END
