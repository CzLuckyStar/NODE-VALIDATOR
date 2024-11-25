


# Create validator

## Export private key
By default, when you run 
`story init` a `validator key` is created for you. To view `your validator key`, run the following command:

```
story validator export
```

In addition, if you want to export the derived `EVM private key` of your validator into the default data config directory, please run the following:

```
story validator export --export-evm-key
```

![image](https://github.com/user-attachments/assets/bef9c618-b1c8-458c-a5f2-255de9054491)


***Important: Please keep your private key in a safe place**

Your EVM Private Key saved to: `/root/.story/story/config/private_key.txt`

Note that to participate in consensus, at least `1 IP` must be staked (equivalent to `1000000000000000000 wei`
)!

Faucet link: https://docs.story.foundation/docs/story-network#-faucets

https://faucet.quicknode.com/story/odyssey

https://faucet.story.foundation/


```
story validator create --stake 1000000000000000000 --private-key "your_private_key"
```

![image](https://github.com/user-attachments/assets/8650eef4-370d-4a62-b0f0-cd1b5cd36ae9)



### Backup the validator key:

File location: `/root/.story/story/config/priv_validator_key.json`

**Copy this file to your local machine.**

Store it carefully; this is the most crucial key for your validator.

# END
