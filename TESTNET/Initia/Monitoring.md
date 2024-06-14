# MONITORING

### CHECK BLOCK SYNC:
OFFICAL:

```
local_height=$(initiad status | jq -r .sync_info.latest_block_height); network_height=$(curl -s https://b545809c-5562-4e60-b5a1-22e83df57748.initiation-1.mesa-rpc.ue1-prod.newmetric.xyz/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```


![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/56e1e067-b540-4624-aa1d-51191badaedf)
