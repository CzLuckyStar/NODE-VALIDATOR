### HOW TO GET A PUBLIC RPC ENDPOINT ON YOUR VALIDATOR NODE to submit the form
Proper format: `http://ip_address:rpc_port`

1. **Fetch your IP and RPC port** (It will print your RPC url)
```bash
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.initia/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC
```
2. **Check if it's responding**
```bash
curl $RPC/status
```
❗ If you are getting `Connection refused` , you need to make it accessible to the Internet:
1. **Edit config**
```bash
sed -i '/\[rpc\]/,/\[/{s/^laddr = "tcp:\/\/127\.0\.0\.1:/laddr = "tcp:\/\/0.0.0.0:/}' $HOME/.initia/config/config.toml
```
2. **Restart your node**
```bash
sudo systemctl restart initiad
```
3. **Ensure logs are good**
```bash
sudo journalctl -u initiad -f -o cat --no-hostname
```
4. **Check if RPC is responding once again**
```bash
curl $RPC/status
```
5. ** Submit the form**
> https://forms.gle/HqLFePaka2NLmzY98
