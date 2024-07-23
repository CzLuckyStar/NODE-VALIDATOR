
## Requirements


- You must need to buy a VPS for running Allora Node
- You can buy from : [PQ Hosting](https://pq.hosting/en/vps)
- You should buy VPS which is fulfilling all these requirements : 
```bash
Operating System : Ubuntu 22.04
CPU : Minimum of 1/2 core
RAM : 2 to 4 GB
Storage : SSD or NVMe with at least 5GB of space
```
## Create Wallet & Request Faucet

- Install : [Keplr Extension](https://chrome.google.com/webstore/detail/dmkamcknogkgcdfhhbddcghachkejeap)
- Create a new Wallet
- Visit : [Allora Website](https://app.allora.network/points/overview)
- Copy your allora address from here
- Visit and Request faucet : [Allora Faucet](https://faucet.testnet-1.testnet.allora.network/)
- If there is an error, try 3-5 times


## Deployment Part 1 (Read Carefully)

- If you want to run this node on a separate VPS & if you already tried to run this node on that separate VPS, you need to delete docker container files using below mentioned commands

- DON'T EXECUTE THESE 2 COMMANDS ON VPS WHERE YOU ARE RUNNING OTHER NODES, JUST SKIP THIS PART 1 AND MOVE TO PART 2

```bash
docker stop $(docker ps -aq) && docker rm $(docker ps -aq) && docker rmi -f $(docker images -aq)
```

```bash
docker rm -f $(docker ps -a -q);docker system prune --volumes -a -f
```

## Deployment Part 2
https://github.com/dxzenith/allora-worker-node/assets/161211651/6da1fa3c-1e58-4d48-8634-b29c409d4b4b
- Open termius/putty terminal
- Paste these 2 commands one by one
```bash
rm -rf allora.sh allora-chain/ basic-coin-prediction-node/
```
```bash
wget https://raw.githubusercontent.com/dxzenith/allora-worker-node/main/allora.sh && chmod +x allora.sh && ./allora.sh
```
- In the middle of the command execution, it will ask for `keyring phrase`, Here you need write a password (example : 12345678)
- During pasting `HEAD_ID` , Don't use `Ctrl+C` to copy and Ctrl+V to paste, instead just select the whole `KEY_ID` and Press `Right Click`


## Check Node Status

Execute the below command to check whether your node is running or not

```bash
  curl --location 'http://localhost:6000/api/v1/functions/execute' \
--header 'Content-Type: application/json' \
--data '{
    "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm",
    "method": "allora-inference-function.wasm",
    "parameters": null,
    "topic": "1",
    "config": {
        "env_vars": [
            {
                "name": "BLS_REQUEST_PATH",
                "value": "/api"
            },
            {
                "name": "ALLORA_ARG_PARAMS",
                "value": "ETH"
            }
        ],
        "number_of_nodes": -1,
        "timeout": 2
    }
}'
```

You will see a response like this :

```bash
{"code":"200","request_id":"876a58bf-2cad-49ff-a722-86a5da444528","results":[{"result":{"stdout":"{\"infererValue\": \"2908.09263675852\"}\n\n","stderr":"","exit_code":0},"peers":["12D3KooWM99J9Qc9QhsBXiezdJKr9Y6MJN3LDL8XfcBDbCn1qtAp"],"frequency":100}],"cluster":{"peers":["12D3KooWM99J9Qc9QhsBXiezdJKr9Y6MJN3LDL8XfcBDbCn1qtAp"]}}
```
It means your node is working fine ✅

## Check for Update

```bash
curl http://localhost:8000/update
```