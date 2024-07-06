# Worker Node

### Requirements : 
Operating System: Ubuntu 22.04
CPU: Minimum of 1/2 core.
Memory: 2 to 4 GB.
Storage: SSD or NVMe with at least 5GB of space.

### Install Update / Packages : 
```
sudo apt update -y && sudo apt upgrade -y
```
```
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y
```

### Install Python3
```
sudo apt install python3
```
```
sudo apt install python3-pip
```

### Install Docker
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt-get update
```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
```
docker version
```

### Install Docker-Compose
```
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
```

```
curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```
chmod +x /usr/local/bin/docker-compose
```
```
docker-compose --version
```
### Docker Permission to User
```
sudo groupadd docker
```
```
sudo usermod -aG docker $USER
```
### Install Go
```
cd $HOME && \
ver="1.22.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

### Install Allorad / Wallet
```
git clone https://github.com/allora-network/allora-chain.git
```
```
cd allora-chain && make all
```
```
allorad version
```
### Add Wallet or Crate Wallet
İf you have `Keplr Wallet` , you can import the wallet.

**Recover your wallet with `seed-phrase`:**

```
allorad keys add testkey --recover
```

**Create A New Wallet:**

```
allorad keys add testkey
```

**Faucet :**

Connect to Allora for found your Allora network adress : `https://app.allora.network/points/campaigns`

You Can Add Allora Nework to Wallet - here : `https://explorer.edgenet.allora.network/wallet/suggest`

`uAllo` faucet : `https://faucet.edgenet.allora.network/`



## Install Worker : 
### Install : 
```
git clone https://github.com/allora-network/basic-coin-prediction-node
```

```
cd basic-coin-prediction-node
```
```
mkdir worker-data
```
```
mkdir head-data
```
**Give Certain Permissions :**
```
sudo chmod -R 777 worker-data
```
```
sudo chmod -R 777 head-data
```

**Create Head Keys:**

```
sudo docker run -it --entrypoint=bash -v ./head-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"
```
```
sudo docker run -it --entrypoint=bash -v ./worker-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"
```

**Copy The `Head - Id`:**

This is `your head ID`, you will need it in the next step

```
cat head-data/keys/identity
```
![image](https://github.com/CzCryptoman/BLOCKCHAIN/assets/153280466/ae64f46b-a3f0-44ea-8dd8-8d0c9f5890dc)

### Connect to Allora Chain : 
Delete and crate a new docker-compose.yml file : 

```
rm -rf docker-compose.yml && nano docker-compose.yml
```
Copy And Paste this code : 
!!!!! Change `head-id` and `Wallet_Seed_Phares` ( 24 Wallet Key ) !!!!

```
version: '3'

services:
  inference:
    container_name: inference-basic-eth-pred
    build:
      context: .
    command: python -u /app/app.py
    ports:
      - "8000:8000"
    networks:
      eth-model-local:
        aliases:
          - inference
        ipv4_address: 172.22.0.4
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/inference/ETH"]
      interval: 10s
      timeout: 5s
      retries: 12
    volumes:
      - ./inference-data:/app/data

  updater:
    container_name: updater-basic-eth-pred
    build: .
    environment:
      - INFERENCE_API_ADDRESS=http://inference:8000
    command: >
      sh -c "
      while true; do
        python -u /app/update_app.py;
        sleep 24h;
      done
      "
    depends_on:
      inference:
        condition: service_healthy
    networks:
      eth-model-local:
        aliases:
          - updater
        ipv4_address: 172.22.0.5

  worker:
    container_name: worker-basic-eth-pred
    environment:
      - INFERENCE_API_ADDRESS=http://inference:8000
      - HOME=/data
    build:
      context: .
      dockerfile: Dockerfile_b7s
    entrypoint:
      - "/bin/bash"
      - "-c"
      - |
        if [ ! -f /data/keys/priv.bin ]; then
          echo "Generating new private keys..."
          mkdir -p /data/keys
          cd /data/keys
          allora-keys
        fi
        # Change boot-nodes below to the key advertised by your head
        allora-node --role=worker --peer-db=/data/peerdb --function-db=/data/function-db \
          --runtime-path=/app/runtime --runtime-cli=bls-runtime --workspace=/data/workspace \
          --private-key=/data/keys/priv.bin --log-level=debug --port=9011 \
          --boot-nodes=/ip4/172.22.0.100/tcp/9010/p2p/head-id \
          --topic=allora-topic-1-worker \
          --allora-chain-key-name=testkey \
          --allora-chain-restore-mnemonic='WALLET_SEED_PHRASE' \
          --allora-node-rpc-address=https://allora-rpc.edgenet.allora.network/ \
          --allora-chain-topic-id=1
    volumes:
      - ./worker-data:/data
    working_dir: /data
    depends_on:
      - inference
      - head
    networks:
      eth-model-local:
        aliases:
          - worker
        ipv4_address: 172.22.0.10

  head:
    container_name: head-basic-eth-pred
    image: alloranetwork/allora-inference-base-head:latest
    environment:
      - HOME=/data
    entrypoint:
      - "/bin/bash"
      - "-c"
      - |
        if [ ! -f /data/keys/priv.bin ]; then
          echo "Generating new private keys..."
          mkdir -p /data/keys
          cd /data/keys
          allora-keys
        fi
        allora-node --role=head --peer-db=/data/peerdb --function-db=/data/function-db  \
          --runtime-path=/app/runtime --runtime-cli=bls-runtime --workspace=/data/workspace \
          --private-key=/data/keys/priv.bin --log-level=debug --port=9010 --rest-api=:6000
    ports:
      - "6000:6000"
    volumes:
      - ./head-data:/data
    working_dir: /data
    networks:
      eth-model-local:
        aliases:
          - head
        ipv4_address: 172.22.0.100


networks:
  eth-model-local:
    driver: bridge
    ipam:
      config:
        - subnet: 172.22.0.0/24

volumes:
  inference-data:
  worker-data:
  head-data:
```
`CTRL X - Y - Enter`. For save.

### Run The Worker : 
```
docker compose build
docker compose up -d
```
### Check Node : 
Check Docker Worker : 
```
docker ps
```
![image](https://github.com/CzCryptoman/BLOCKCHAIN/assets/153280466/d5d130ac-8eca-43c0-915d-3a3f7997743d)

Replace `container id` with `your container id` : 
```
docker logs -f CONTAINER_ID
```
Success: `register node Tx Hash:=82BF67E2E1247B226B8C5CFCF3E4F41076909ADABF3852C468D087D94BD9FC3B`
![image](https://github.com/CzCryptoman/BLOCKCHAIN/assets/153280466/168febf1-4edb-495c-91b1-ecab1aaf68ac)


### Check running node : 
```
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
Response : 
```
{
  "code": "200",
  "request_id": "03001a39-4387-467c-aba1-c0e1d0d44f59",
  "results": [
    {
      "result": {
        "stdout": "{\"value\":\"2564.021586281073\"}",
        "stderr": "",
        "exit_code": 0
      },
      "peers": [
        "12D3KooWG8dHctRt6ctakJfG5masTnLaKM6xkudoR5BxLDRSrgVt"
      ],
      "frequency": 100
    }
  ],
  "cluster": {
    "peers": [
      "12D3KooWG8dHctRt6ctakJfG5masTnLaKM6xkudoR5BxLDRSrgVt"
    ]
  }
}
```
### Check Update : 
```
curl http://localhost:8000/update
```
Response : 
`0`
### Check the inference node :
```
curl http://localhost:8000/inference/ETH
```
Response : 
`{"value":"2564.021586281073"}`
