
# Upgrade Warden Version: 0.4.0

## X86

```
sudo systemctl stop wardend
cd $HOME
rm -rf warden
rm -rf /usr/local/bin/wardend
```

```
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.4.0/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip && rm -rf wardend_Linux_x86_64.zip
chmod +x wardend
sudo mv wardend /usr/local/bin
wardend version
sudo systemctl restart wardend
sudo journalctl -u wardend -f --no-hostname -o cat
```


## ARM

```
sudo systemctl stop wardend
cd $HOME
rm -rf warden
rm -rf /usr/local/bin/wardend
```

```
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.4.0/wardend_Linux_arm64.zip
unzip wardend_Linux_arm64.zip && rm -rf wardend_Linux_arm64.zip
chmod +x wardend
sudo mv wardend /usr/local/bin
wardend version
```

```
sudo systemctl restart wardend
sudo journalctl -u wardend -f --no-hostname -o cat
```

# END
