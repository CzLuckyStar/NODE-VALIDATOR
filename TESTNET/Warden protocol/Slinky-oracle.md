
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
sudo tee /etc/systemd/system/warden-slinky.service > /dev/null <<EOF
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

## Start slinky (oracle) service
```
sudo systemctl daemon-reload
sudo systemctl enable warden-slinky
sudo systemctl restart warden-slinky.service && sudo journalctl -u warden-slinky.service -f --no-hostname -o cat
```

## CHECK LOG & STATUS:

To check service logs use command below:

```
journalctl -fu warden-slinky -o cat
```


```
sudo systemctl status warden-slinky
```

Check price:
```
curl localhost:$SLINKY_PORT/slinky/oracle/v1/prices | jq
```
