**Step 1: Update system**
```php
sudo apt update && sudo apt upgrade -y
```

**Step 2: Install packages**
```php
sudo apt install make clang pkg-config libssl-dev build-essential git screen protobuf-compiler -y
```

**Step 3: Install Rust**
```php
curl https://sh.rustup.rs -sSf | sh
```

**Select 1 and enter**

**Step 4: Go to home**
```php
source $HOME/.cargo/env
```

**Step 5: Update rust**
```php
rustup update nightly
```

**Step 6: Add tools**
```php
rustup target add wasm32-unknown-unknown --toolchain nightly
```
**Step 8: Access Avail folder**
```php
mkdir avail
cd avail
```
**Step 9: Download binaries**
```php
wget https://github.com/availproject/avail/releases/download/v2.1.0.0-rc1/x86_64-ubuntu-2204-avail-node.tar.gz
```
**Step 10: Extract**
```php
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz
```
**Step 11: run node**
```php
./avail-node --chain turing --name "Yourname" --validator -d ./node-data
```
CTRL + C to escape

**Step 12: Edit service file**
```php
sudo nano /etc/systemd/system/availd.service
```
*Copy and Paste the content of service file as:*
*Replace yourname in “Yourname”.*
```php
[Unit]
Description=Avail Validator
After=network.target
StartLimitIntervalSec=0
[Service]
User=root
ExecStart= /root/avail/avail-node -d /root/avail/node-data --chain turing --validator --name "Your-Name"
Restart=always
RestartSec=120
[Install]
WantedBy=multi-user.target
```
**Step 13: Enable the service file**
```php
sudo systemctl daemon-reload
sudo systemctl enable availd.service
```

**Step 14: Start service file**
```php
sudo systemctl start availd.service
```

**Step 15: Check status of service**
```php
sudo systemctl status availd.service
```
**Step 16: Check logs**
```php
journalctl -f -u availd
```
