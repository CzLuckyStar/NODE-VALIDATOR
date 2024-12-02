

# Command
## Delegate to Yourself
```
story validator stake \
  --validator-pubkey Akn2LfO1JjJkoMB6wJpr02WqwVcaa0yxxxxxxxxx \
  --stake 5000000000000000000 \
  --private-key "your_private_key"
```


## Validator Unstaking
```
story validator unstake \
  --validator-pubkey ${VALIDATOR_PUB_KEY_IN_BASE64} \
  --unstake ${AMOUNT_TO_UNSTAKE_IN_WEI} \
  --private-key "your_private_key"
```

## Validator Stake-on-behalf
```
story validator stake \
   --delegator-pubkey An/k/1HntF2r8ZaqrwxEOTKSIXdVsZk5wxkt4i5uhk3V \
   --validator-pubkey A0PSykpcrXXomPnNJcITuCawqg+G0JokQBGd9hU2f5CM \
   --stake 1000000000000000000 \
   --private-key "your_private_key"
```


## Validator Unstake-on-behalf
```
story validator unstake-on-behalf \
  --delegator-pubkey ${DELEGATOR_PUB_KEY_IN_BASE64} \
  --validator-pubkey ${VALIDATOR_PUB_KEY_IN_BASE64} \
  --unstake ${AMOUNT_TO_STAKE_IN_WEI} \
  --private-key "your_private_key"
```



## Check block sync left:
```
while true; do
    local_height=$(curl -s localhost:14657/status | jq -r '.result.sync_info.latest_block_height');
    network_height=$(curl -s https://odyssey.storyrpc.io/status | jq -r '.result.sync_info.latest_block_height');
    blocks_left=$((network_height - local_height));
    echo -e "\033[1;38mYour node height:\033[0m \033[1;34m$local_height\033[0m | \033[1;35mNetwork height:\033[0m \033[1;36m$network_height\033[0m | \033[1;29mBlocks left:\033[0m \033[1;31m$blocks_left\033[0m";
    sleep 5;
done
```

# END
