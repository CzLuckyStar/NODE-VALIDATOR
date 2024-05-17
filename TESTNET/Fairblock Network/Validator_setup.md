# Validating on Testnet
Before creating a testnet validator, ensure you have first followed the instructions on running the testnet node.

Create Wallet
If you decided to participate in the validator set, you will first need a wallet added to your node. You can create a wallet with following command (Replace validator with a name of your choosing):
```
fairyringd keys add validator
```
Ensure you write down the mnemonic as you can not recover the wallet without it. To ensure the wallet you created was saved to your node, run the following command to see if your wallet is in the list:
```
fairyringd keys list
```
Create validator command
Make sure that your wallet has enough tokens to become validator. You may check your balance by the following command (Replace ADDRESS with the wallet address you just created):
```
fairyringd q bank balances ADDRESS
```
Here is the command for creating a validator:
```
fairyringd tx staking create-validator \
  --amount `10000000000ufairy` \
  --commission-max-change-rate 0.01 \
  --commission-max-rate 0.2 \
  --commission-rate 0.1 \
  --from [ACCOUNT_KEY_NAME] \
  --min-self-delegation 1 \
  --moniker [validator_moniker] \
  --security-contact "[optional-security@example.com]" \
  --website [optional.web.page.com] \
  --details [optional-details] \
  --pubkey $(fairyringd tendermint show-validator) \
  --chain-id fairyring-testnet-1
```
Explanation for each of the command flags:
```
amount is the amount you stake in your own validator (in this example it is 10000000000ufairy)
commission-max-change-rate is how much you can increase your commission rate in a 24 hour period (in the example above, 1% per day until reaching the max rate)
commission-max-rate is the most you are allowed to charge your delegates (in the example above, 20%)
commission-rate is the rate you will charge your delegates (in the example above, 5%)
from is the account name of the wallet you just created
min-self-delegation is the lowest amount of personal funds the validator is required to have in their own validator to stay bonded (in the example above, 1ufairy)
moniker is your validator name
security-contact is your email address
website is the URL of your website
details is detail of your validator node
pubkey is the validator public key
chain-id in this case is fairyring-testnet-1, or whatever chain-id you are working with
```
If you are joining the validator set after the genesis creation, that will be all you need to do.

If you are joining the validator set before the genesis creation, here is the steps on creating the gentx:

Create Gentx
Create a genesis transaction to become validator:
```
fairyringd gentx \
  [account_key_name] \
  10000000000ufairy \
  --commission-max-change-rate 0.01 \
  --commission-max-rate 0.2 \
  --commission-rate 0.05 \
  --min-self-delegation 1 \
  --details [optional-details] \
  --identity [optional-identity] \
  --security-contact "[optional-security@example.com]" \
  --website [optional.web.page.com] \
  --moniker [node_moniker] \
  --chain-id fairyring-testnet-1
```
If you would like to know the explanation on each of flags, please see the explanation above.

After running the command above, it will create a gentx-XXXXXX.json file under this directory:
```
$HOME/.fairyring/config/gentx/gentx-XXXXXX.json
```
Copy the contents inside $HOME/.fairyring/config/gentx/gentx-XXXXXX.json to fairyring/gentxs/ directory (replace VALIDATOR_NAME to your validator name):
```
cp $HOME/.fairyring/config/gentx/gentx-*.json $HOME/fairyring/networks/testnets/fairyring-testnet-1/gentxs/gentx-VALIDATOR_NAME.json
```

Create a json file with all your node information like the example below, and name it peers-VALIDATOR_NAME.json (replace VALIDATOR_NAME to your validator name)
```
{
  "node_id": "YOUR_NODE_ID",
  "public_ip": "YOUR_IP",
  "port": "YOUR_PORT"
}
```
You can get your node_id by the following command:
```
fairyringd tendermint show-node-id
```
You can get your public_ip by the following command:
```
curl ipinfo.io/ip
```
For your port, the default is 26656 if you did not change the config.

After creating the file, put it under $HOME/fairyring/networks/testnets/fairyring-testnet-1/peers/ directory

Create a new branch with the peers & gentx files, make a commit and push the update to Github.
```
git switch -c your-branch-name
git commit -m "commit message"
git push -u origin your-branch-name
```
Create a Pull Request to the main branch on fairyring repo
