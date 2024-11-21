

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

# END
