
# VALIDATOR:

### Run your own validator

Show PubKey:
```
kopid comet show-validator
```

OR:
```
kopid tendermint show-validator
```

We assume operators understand how to create keys using the CLI. Below is how to register your validator to the Kopi blockchain. First you need to create a text file contain your validator’s configufation:

```
cd $HOME/.kopid/config
nano validator.json
```

```
{
  "pubkey": {
    "@type":"/cosmos.crypto.ed25519.PubKey",
    "key":"<replace with value from:priv_validator_key.json>"
  },
  "amount": "1000000ukopi",
  "moniker": "YOUR_MONKIER",
  "identity": "YOUR_KEYBASE_ID",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_EMAIL_ADDRESS",
  "details": "Wiv-Sugar",
  "commission-rate": "0.1",
  "commission-max-rate": "0.2",
  "commission-max-change-rate": "0.01",
  "min-self-delegation": "1"
}
```

### Then create your validator by passing the created text file:
```
kopid tx staking create-validator validator.json \
  --from wallet \
  --keyring-backend file \
  --chain-id kopi-test-1
```
# END VALIDATOR
