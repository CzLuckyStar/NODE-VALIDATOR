# Metrics
fairyring & fairyringclient provide support for metrics to allow users to monitor the health, performance and status of fairyring & fairyringclient.

### fairyring setup
`fairyring` uses `Prometheus` to publish metrics, It can be enabled through the `config.toml` file.
```
#######################################################
###       Instrumentation Configuration Options     ###
#######################################################
[instrumentation]

# When true, Prometheus metrics are served under /metrics on
# PrometheusListenAddr.
# Check out the documentation for the list of available metrics.
prometheus = true

# Address to listen for Prometheus collector(s) connections
prometheus_listen_addr = ":26660"

# Maximum number of simultaneous connections.
# If you want to accept a larger number than the default, make sure
# you increase your OS limits.
# 0 - unlimited.
max_open_connections = 3

# Instrumentation namespace
namespace = "fairyring"
```
After enabling metrics in `config.toml`, If you restart your node, you can check if the metrics are working by:
```
curl localhost:26660/metrics
```

### fairyringclient setup
`fairyringclient` enables metrics on `port :2222` by default, you can check your metrics port by:
```
fairyringclient config show
```
Example Output:
```
Using config file: /Users/fairblock/.fairyringclient/config.yml
GRPC Endpoint: 127.0.0.1:9090
FairyRing Node Endpoint: http://127.0.0.1:26657
Chain ID: fairyring-testnet-1
Chain Denom: ufairy
InvalidSharePauseThreshold: 5
MetricsPort: 2222
```
You can update the metrics port by the following command:
```
fairyringclient config update --metrics-port NEW_PORT
```
### Prometheus installation and Setup
After enabling metrics on fairyring node / fairyringclient, you will need a program to scrape its data, we suggest using Prometheus in this example.

Install Prometheus by following the Official Installation guide in the same machine as your node.
Create a `Prometheus` config file, we will create one at `$HOME/.fairyring/config/prometheus.yml` in this example.
The config file should look something like this:
```
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'Fairyring'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:26660']
  - job_name: 'FairyringClient'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:2222']
```
### Start Prometheus server:
```
prometheus --config.file="$HOME/.fairyring/config/prometheus.yml" --web.listen-address="0.0.0.0:2112"
```
`Prometheus` runs its server on `port 9090` by default, we suggest to run `Prometheus` in a different port, `2112` in this case, to avoid conflict with the `fairyring node` `gRPC port` (`9090` by default`).

### Visualization
After installing `Prometheus` and enabling `fairyring` metrics, you will need something to visualize the data. We suggest using Grafana for building a dashboard to visualize the data.

You can use their cloud service of run it yourself. You can follow their Official Installation Guide for a tutorial on installing `Grafana`. After installing `Grafana`, you can start it by:
```
grafana server
```
The command above will start a server on `localhost:3000`.

After you started a `Grafana` server, you can now create your own dashboard or import the one we already created for `fairyring` & `fairyringclient`.

