# Upgrade v0.4.2

```
cd $HOME
rm -rf download
mkdir download
cd download
wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.4.2/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip
rm wardend_Linux_x86_64.zip
chmod +x $HOME/download/wardend
sudo mv $HOME/download/wardend $(which wardend)
sudo systemctl restart wardend && sudo journalctl -u wardend -f
```
