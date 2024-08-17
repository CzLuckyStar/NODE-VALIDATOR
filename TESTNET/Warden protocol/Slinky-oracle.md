
# ORACLE

## Clone repo
```
sudo git clone https://github.com/skip-mev/slinky.git
cd slinky
sudo git checkout v1.0.9
sudo make install
```

## Dont change
```
GRPC=$(grep -A 10 '\[grpc\]' $HOME/.warden/config/app.toml | grep 'address' | grep -oP '(?<=address = ")[^"]+')
```

## Change if you need

```
SLINKY_PORT=8080 #8080 default
```

## Add oracle in you app.toml
```
sudo tee -a $HOME/.warden/config/app.toml <<EOF
[oracle]

enabled = "true"
oracle_address = "localhost:${SLINKY_PORT}"
client_timeout = "2s"
metrics_enabled = "true"
EOF
```


## Restart wardend service
```
sudo systemctl restart wardend.service && sudo journalctl -u wardend.service -f --no-hostname -o cat
```


## Create slinky systemd service
```
sudo tee /etc/systemd/system/slinkyd.service > /dev/null <<EOF
[Unit]
Description=Slinky service
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME
ExecStart=$(which slinky) --market-map-endpoint $GRPC --log-file $HOME/sidecar.log --port $SLINKY_PORT
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

## Start slinky service
```
sudo systemctl daemon-reload
sudo systemctl enable slinkyd
sudo systemctl restart slinkyd.service && sudo journalctl -u slinkyd.service -f --no-hostname -o cat
```
