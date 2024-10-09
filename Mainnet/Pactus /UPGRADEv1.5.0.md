# 20241009, UPGRADE V1.5.0 PACTUS 

```
cd $HOME/.pactus/
rm -rf build
wget https://github.com/pactus-project/pactus/releases/download/v1.5.0/pactus-cli_1.5.0_linux_amd64.tar.gz && 
tar -xzf pactus-cli_1.5.0_linux_amd64.tar.gz && 
rm -rf pactus-cli_1.5.0_linux_amd64.tar.gz && 
mv pactus-cli_1.5.0 build
```

Restart service:
```
sudo systemctl daemon-reload
sudo systemctl enable pactusd
sudo systemctl restart pactusd
```
