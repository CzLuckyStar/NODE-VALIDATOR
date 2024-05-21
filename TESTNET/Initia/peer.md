### NON CONTABO PEERS (225)
```bash
PEERS=$(curl -s --max-time 3 --retry 2 --retry-connrefused "https://rpc-initia-testnet.trusted-point.com/non_contabo_peers.txt")
```
### HETZNER PEERS (45)
```bash
PEERS=$(curl -s --max-time 3 --retry 2 --retry-connrefused "https://rpc-initia-testnet.trusted-point.com/hetzner_peers.txt")
```
> Choose any command above and fetch peers: 
```bash
if [ -z "$PEERS" ]; then
    echo "No peers were retrieved from the URL."
else
    echo -e "\nPEERS: "$PEERS""
    sed -i "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" "$HOME/.initia/config/config.toml"
    echo -e "\nConfiguration file updated successfully.\n"
fi
```
> Stop the node
```bash
sudo systemctl stop initiad
```
> Delete addrbook.json
```bash
sudo rm $HOME/.initia/config/addrbook.json
```
> Start the node
```bash
sudo systemctl restart initiad
```
> Check logs
```bash
sudo journalctl -u initiad -f -o cat --no-hostname
```
> Check sync
```bash
initiad status | jq -r .sync_info
```
