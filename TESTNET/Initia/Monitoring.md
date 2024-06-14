# MONITORING

### Introduction
Prometheus and Grafana are powerful tools for monitoring your node's performance and health.

Prometheus is an open-source monitoring system that collects metrics from various sources, while Grafana is a visualization platform that allows you to create interactive dashboards based on the collected metrics.

By setting up Prometheus and Grafana, you can gain valuable insights into your node's blockchain-related data, system information, and slinky sidecar performance.

### Install Node Exporter
Node Exporter is a Prometheus exporter that exposes hardware and OS metrics for monitoring purposes. It provides detailed information about your node's system resources, such as CPU usage, memory utilization, disk space, and network traffic.

To install Node Exporter and run it as a service on your validator server:

Connect to your validator server via SSH.

Run the following script to install Node Exporter:
```
# Install the necessary packages
sudo apt -q update
sudo apt -qy install curl wget

# Define the latest version of node_exporter
VERSION=$(curl -s https://api.github.com/repos/prometheus/node_exporter/releases/latest | grep 'tag_name' | cut -d\" -f4 | cut -c2-)

# Determine the operating system
if [[ "$(uname -s)" == "Linux" ]]; then
  OS="linux"
elif [[ "$(uname -s)" == "Darwin" ]]; then
  OS="darwin"
else
  echo "Unsupported operating system"
  exit 1
fi

# Determine the architecture
if [[ "$(uname -m)" == "x86_64" ]]; then
  ARCH="amd64"
elif [[ "$(uname -m)" == "aarch64" ]]; then
  ARCH="arm64"
else
  echo "Unsupported architecture"
  exit 1
fi

# Download and install node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v${VERSION}/node_exporter-${VERSION}.${OS}-${ARCH}.tar.gz
tar xvf node_exporter-${VERSION}.${OS}-${ARCH}.tar.gz
rm node_exporter-${VERSION}.${OS}-${ARCH}.tar.gz
sudo mv node_exporter-${VERSION}.${OS}-${ARCH} node_exporter
chmod +x $HOME/node_exporter/node_exporter
sudo mv $HOME/node_exporter/node_exporter /usr/bin
rm -Rvf $HOME/node_exporter/

# Create a systemd service for node_exporter
sudo tee /etc/systemd/system/exporterd.service > /dev/null <<EOF
[Unit]
Description=node_exporter
After=network-online.target
[Service]
User=$USER
ExecStart=/usr/bin/node_exporter
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# Enable and start the node_exporter service
sudo systemctl daemon-reload
sudo systemctl enable exporterd
sudo systemctl start exporterd
```
_By default, Node Exporter listens on port 9100._

**Enable Prometheus and Oracle Metrics**
Enable Prometheus metrics by modifying the config.toml file:
```
sed -i -e "s/prometheus *=.*/prometheus = true/g" $HOME/.initia/config/config.toml
```
Enable Oracle metrics by modifying the app.toml file:
```
sed -i -e "s/metrics_enabled *=.*/metrics_enabled = true/g" $HOME/.initia/config/app.toml
```
Restart your node for the changes to take effect:
```
sudo systemctl restart initiad
```
### Install Docker
Run the following command to download and execute the Docker installation script:
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
(Optional) To manage Docker as a non-root user, you can add your user to the docker group:
```
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```
This step is recommended for security reasons, as it allows you to run Docker commands without using sudo.

Verify the Docker installation by running the following command:
```
docker run hello-world
```
If Docker is installed correctly, you should see a `"Hello from Docker!"` message.

### Alerts via Telegram Bot
To receive alerts via Telegram, you'll need to create a new bot and obtain an API token.

Start a conversation with @BotFather: `https://t.me/BotFather` and send the /newbot command to create a new bot. Follow the prompts until you receive the API token. Save this for later.
Start a conversation with @userinfobot: `https://t.me/userinfobot` bot and send the /start command. The bot will respond with your user ID. Save this ID for later use.
**Install Monitoring Tool**
Clone the monitoring tool repository to your home directory:
```
cd ~
git clone https://github.com/nodejumper-org/monitoring-tool.git
```
Change to the monitoring-tool directory and copy the example configuration files:
```
cd monitoring-tool
cp prometheus/prometheus.yml.example prometheus/prometheus.yml
cp alertmanager/config.yml.example alertmanager/config.yml
```
Download the slinky dashboards:
```
cd ~/monitoring-tool/grafana/provisioning/dashboards
wget https://raw.githubusercontent.com/skip-mev/slinky/main/grafana/provisioning/dashboards/chain-dashboard.json
wget https://raw.githubusercontent.com/skip-mev/slinky/main/grafana/provisioning/dashboards/side-car-dashboard.json
```
Edit the `config.yml` file and replace the chat_id and bot_token values with your Telegram user ID and bot token obtained earlier:
```
chat_id=1111111                 # your telegram user id
bot_token=11111111:AAG_XXXXXXX  # your telegram bot token
```
Edit the `prometheus.yml` file and replace the targets with your node's IP address and ports for Node Exporter, Prometheus, and slinky. The default ports are 9100, 26660, and 8002, respectively:
```
# example for servers with node_exporter and cosmos-based node installed
  - job_name: "initia-validator"
    static_configs:
    - targets: ["192.0.0.1:9100","192.0.0.1:26660","192.0.0.1:8002"]
      labels:
        instance: "validator"
```
Start the monitoring tool containers using Docker Compose:
```
docker compose up -d
```
**Accessing Grafana Dashboard**
Open a web browser and navigate to `http://<your_server_ip>:3000`. Log in using the default credentials:

`Username: admin Password: admin`

Once logged in, you can explore the following pre-configured dashboards:

CosmosSDK Based Chains Dashboard: Displays your node's blockchain-related data.
Node Exporter for Prometheus Dashboard: Shows your node's system information and resource utilization.
Slinky Chain Dashboard: Provides an overview of the blockchain and on-chain oracle summary.
Slinky Sidecar Dashboard: Displays information about your node's slinky sidecar performance.



### CHECK BLOCK SYNC:
OFFICAL:

```
local_height=$(initiad status | jq -r .sync_info.latest_block_height); network_height=$(curl -s https://b545809c-5562-4e60-b5a1-22e83df57748.initiation-1.mesa-rpc.ue1-prod.newmetric.xyz/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```


![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/56e1e067-b540-4624-aa1d-51191badaedf)
