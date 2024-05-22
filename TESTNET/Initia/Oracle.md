## Oracle
Download and install:
```
cd "$HOME" && git clone https://github.com/skip-mev/slinky.git && cd slinky && \
git checkout v0.4.3 && make build && \
ln -s "$HOME/slinky/build/slinky" "/usr/bin/"
```
Update app.toml:
```
sed -i '0,/^enabled *=/{//!b};:a;n;/^enabled *=/!ba;s|^enabled *=.*|enabled = "true"|' $HOME/.initia/config/app.toml && \
sed -i -e 's|^oracle_address *=.*|oracle_address = "127.0.0.1:8080"|' $HOME/.initia/config/app.toml && \
sed -i -e 's|^client_timeout *=.*|client_timeout = "300ms"|' $HOME/.initia/config/app.toml
```
Use Oracle at service:
```
tee /etc/systemd/system/oracle.service > /dev/null <<EOF
[Unit]
Description=oracle

[Service]
Type=simple
User=$USER
ExecStart=/usr/bin/slinky --oracle-config-path "$HOME/slinky/config/core/oracle.json" --market-map-endpoint 0.0.0.0:9090
Restart=on-abort
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=oracle
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
Launch oracle and restart node:
```
sudo systemctl daemon-reload && \
sudo systemctl enable oracle && \
sudo systemctl restart oracle && \
sudo systemctl restart initiad && \
sudo journalctl -u oracle -f -o cat
```
