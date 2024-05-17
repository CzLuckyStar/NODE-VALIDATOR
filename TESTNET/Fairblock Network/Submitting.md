# Submitting keyshares to fairyring
This tutorial goes over on different ways to submit keyshares to fairyring and in particular, to the keyshare module. Make sure you have installed fairyring prior to following the instructions below.

### Requirements
In order to be able to submit keyshare to fairyring, there are few requirements:

You are a validator in the staking module with a minimum 10000000000ufairy staked. Check if you are currently a validator as well as how many tokens you have staked with the following command:
```
fairyringd q staking validators 
```
You registered as a validator in the fairyring keyshare module. Check if you are in the validator set with following command:
```
fairyringd q keyshare list-validator-set
```
You can register as a validator in the keyshare module with the following command (replace YOUR_WALLET_NAME with your account name in your keyring):
```
fairyringd tx keyshare register-validator --from YOUR_WALLET_NAME
```
NOTE: If you are submitting keyshares with fairyringclient, you can skip registering as a validator because the client will do it for you.

### fairyringclient
fairyringclient is a tool which:

Fetches your master keyshare from our ShareGenerationClient
Computes the derived keyshare every block
Automatically submits the derived keyshare to fairyring every block.
This is the recommended way to submit keyshares. Make sure you have completed the steps in the prerequisites prior to following the instructions below.

Download & Install fairyringclient by the following command:
```
cd $HOME
git clone https://github.com/Fairblock/fairyringclient.git
cd fairyringclient
go mod tidy
go install
```
Initialize the fairyringclient config:
```
fairyringclient config init
```
Check the client config:
```
fairyringclient config show
```
Example output of the command:
```
Using config file: /Users/fairblock/.fairyringclient/config.yml
GRPC Endpoint: 127.0.0.1:9090
FairyRing Node Endpoint: http://127.0.0.1:26657
Chain ID: fairyring-testnet-1
Chain Denom: ufairy
InvalidSharePauseThreshold: 5
MetricsPort: 2222
```
Update the client config:
```
fairyringclient config update
```
Here are the flags for updating different field of the config:
```
--chain-id CHAIN_ID, CHAIN_ID is the chain id you are working with, the current default is fairyring-testnet-1
--denom DENOM, DENOM is the token denom of the chain, default is ufairy
--grpc-port PORT, PORT is the gRPC port of the node you are connecting to, default is 9090
--ip IP, IP is the IP address of the node you are connecting to, default is "127.0.0.1"
--metrics-port METRICS_PORT, METRICS_PORT is the port for Prometheus metrics to listen to, default is 2222
--pause-threshold THRESHOLD, stops the client if it submits THRESHOLD invalid keyshares in a row, default is 5
--port NODE_PORT, NODE_PORT is the port of the node you are connecting to, default is 26657
--protocol PROTOCOL, PROTOCOL is the protocol of the node you are connecting to, default is "http"
```
Add validator account private key
```
fairyringclient keys set "private key in hex"
```
### The private key should be the validator address that is staking on fairyring chain

Example:
```
> fairyringclient keys set f5c691d4b53ec8c3a3ad35e88525f9b8f33d307b3414c93f1b856265409a3a04
Using config file: /Users/fairblock/.fairyringclient/config.ymlSuccessfully added cosmos private key to config!
```

Check the key is added to the client correctly:
```
> fairyringclient keys show  
Using config file: /Users/fairblock/.fairyringclient/config.yml
Private Key: f5c691d4b53ec8c3a3ad35e88525f9b8f33d307b3414c93f1b856265409a3a04  
```
The [0] at the beginning of the key is the index of your key, which is used to delete the key

Start the client:
```
fairyringclient start
```
The log will look something like this when it started submitting keyshare:
```
2023/01/01 00:00:00 Latest Block Height: 12345 | Deriving Share for Height: 12346                
2023/01/01 00:00:00 Current Share Expires at: 20000 | {01 5a3cc54cb8fc2cae480a58d2922d84d69376726ed859615c6bd869154cb4ccc2}                
2023/01/01 00:00:00 [0] Submit KeyShare for Height 12345 Confirmed
```

### Troubleshooting....
