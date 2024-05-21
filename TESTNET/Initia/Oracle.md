# Oracle

## Step 1: Clone the Repository
Clone the slinky repository.

```
git clone https://github.com/skip-mev/slinky.git
cd slinky
```
## checkout proper version
```
git checkout v0.4.3
```

# Step 2: Running The Sidecar
## Config Setup
The recommended oracle component can be found in `config/core/oracle.json` within the Slinky repo.

// config/core/oracle.json found in the repo.

```json
{
  ...,
    {
      "name": "marketmap_api",
      "api": {
        "enabled": true,
        "timeout": 20000000000,
        "interval": 10000000000,
        "reconnectTimeout": 2000000000,
        "maxQueries": 1,
        "atomic": true,
        "url": "0.0.0.0:9090", // URL that must point to a node GRPC endpoint
        "endpoints": null,
        "batchSize": 0,
        "name": "marketmap_api"
      },
      "type": "market_map_provider"
    }
  ],
  "metrics": {
    "prometheusServerAddress": "0.0.0.0:8002",
    "enabled": true
  },
  "host": "0.0.0.0",
  "port": "8080"
}
```

## Starting The Sidecar
Initiating the oracle sidecar process is accomplished by executing the binary.

## Build the Slinky binary in the repo.
```
make build
```
## Run with the core oracle config from the repo.
```
./build/slinky --oracle-config-path ./config/core/oracle.json --market-map-endpoint 0.0.0.0:9090
```

# Step 3: Validating Prices
```
make run-oracle-client
```

# Step 4: Enable Oracle Vote Extension

In order to utilize the Slinky oracle data in the Initia node, the Oracle setting should be enabled in the config/app.toml file.
```
cd $HOME/.initia/config
nano app.toml
```

```
###############################################################################
###                                  Oracle                                 ###
###############################################################################
[oracle]
# Enabled indicates whether the oracle is enabled.
enabled = "true"

# Oracle Address is the URL of the out of process oracle sidecar. This is used to
# connect to the oracle sidecar when the application boots up. Note that the address
# can be modified at any point, but will only take effect after the application is
# restarted. This can be the address of an oracle container running on the same
# machine or a remote machine.
oracle_address = "127.0.0.1:8080"

# Client Timeout is the time that the client is willing to wait for responses from 
# the oracle before timing out.
client_timeout = "500ms"

# MetricsEnabled determines whether oracle metrics are enabled. Specifically
# this enables instrumentation of the oracle client and the interaction between
# the oracle and the app.
metrics_enabled = "false"
```




