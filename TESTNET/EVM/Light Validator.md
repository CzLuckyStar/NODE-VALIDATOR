# andes-doc ![image](https://github.com/user-attachments/assets/08d1b397-4a1c-43d1-8bf9-f9beea7e8319)


# Andes Network

Andes testnet is a testnet focused on enabling validators to test out their infrastructure by running nodes connected to the network. 

| Network Name | Dill Testnet Andes |
| --- | --- |
| RPC URL | https://rpc-andes.dill.xyz/ |
| Chain ID | 558329 |
| Currency symbol | DILL |
| Explorer URL | https://andes.dill.xyz/ |

## **Hardware** Requirement

Below are the minimum hardware requirements to run **Dill** **light validator node.** It is also part of the consensus network, requires staking and earns `DILL` token for reward.

|  | Minimum |
| --- | --- |
| CPU | 2 cores |
| Memory | 2G |
| Disk | 20G |
| Network | 1MB/s |

If you are using cloud computing platform such as AWS EC2, you can check different instance configurations at https://aws.amazon.com/ec2/instance-types/ 

Or alternatively, you can use AWS Lightsail, which now offers the first 90 days free for the `2 GB Memory, 2 vCPUs Processing, 60 GB SSD Storage, 3 TB Transfer` instances. More details are at https://lightsail.aws.amazon.com/ls/webapp/home/instances 

## Recommended OS

Ubuntu 22.04 (We are working on support for other Linux-like OS)

MacOS

## PORT:

![image](https://github.com/user-attachments/assets/cc7e9baa-c9ea-44a9-8292-40995598c0f7)


## Run a light validator

Light validator is a type of node that performs availability validation solely through data sampling without participating in data sharding synchronization. It is also part of a consensus network. These nodes can participate in voting but will not act as proposers to generate new blocks. You can follow the steps below to start a light validator:

1. Download the light validator binary package from:
    - General Linux-like [https://dill-release.s3.ap-southeast-1.amazonaws.com/dill.tar.gz](https://dill-release.s3.ap-southeast-1.amazonaws.com/linux/dill.tar.gz)
    - MaxOS specific - https://dill-release.s3.ap-southeast-1.amazonaws.com/macos/dill.tar.gz
    
    or simply run from your command line terminal:
   ```
   curl -O https://dill-release.s3.ap-southeast-1.amazonaws.com/linux/dill.tar.gz
   ```
   
    
3. Extract the package:
    
    ```
   tar -xzvf dill.tar.gz && cd dill
    ```
    
    sample output
    
    ```bash
    ubuntu@ip-xxxx:~$ curl -O https://dill-release.s3.ap-southeast-1.amazonaws.com/linux/dill.tar.gz
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100 64.5M  100 64.5M    0     0  6655k      0  0:00:09  0:00:09 --:--:-- 8072k
    ubuntu@ip-xxxx:~$ tar -xzvf dill.tar.gz && cd dill
    dill/
    dill/validators.json
    dill/genesis.ssz
    dill/dill_validators_gen
    dill/start_light.sh
    dill/dill-node
    ubuntu@ip-xxxx:~/dill$
    ```
    
5. Generate validator keys using the key generation tool:
    
    ```
   ./dill_validators_gen new-mnemonic --num_validators=1 --chain=andes --folder=./
    ```
    
    sample output
    
    ```bash
    ubuntu@ip-xxxx:~/dill$ ./dill_validators_gen new-mnemonic --num_validators=1 --chain=andes --folder=./
    
    ***Using the tool on an offline and secure device is highly recommended to keep your mnemonic safe.***
    
    Please choose your language ['1. العربية', '2. ελληνικά', '3. English', '4. Français', '5. Bahasa melayu', '6. Italiano', '7. 日本語', '8. 한국어', '9. Português do Brasil', '10. român', '11. Türkçe', '12. 简体中文']:  [English]: 3
    Please choose the language of the mnemonic word list ['1. 简体中文', '2. 繁體中文', '3. čeština', '4. English', '5. Italiano', '6. 한국어', '7. Português', '8. Español']:  [english]: 4
    Create a password that secures your validator keystore(s). You will need to re-enter this to decrypt them when you setup your Dill validators.:
    Repeat your keystore password for confirmation:
    The amount of DILL token to be deposited(2500 by default). [2500]:
    This is your mnemonic (seed phrase). Write it down and store it safely. It is the ONLY way to retrieve your deposit.
    
    Creating your keys.
    Creating your keystores:	  [####################################]  1/1
    Verifying your keystores:	  [####################################]  1/1
    Verifying your deposits:	  [####################################]  1/1
    
    Success!
    Your keys can be found at: ./validator_keys
    
    Press any key.
    ubuntu@ip-xxxx:~/dill$
    ```
    
    This will generate the validator keys and save them in  `./validator_keys` directory 
    
    ```bash
    ubuntu@ip-xxxxx:~/dill$ ls -ltr ./validator_keys
    total 8
    -r--r----- 1 ubuntu ubuntu 710 Jul 13 08:06 keystore-m_12381_3600_0_0_0-xxxxxx.json
    -r--r----- 1 ubuntu ubuntu 706 Jul 13 08:06 **deposit_data-xxxx.json**
    ubuntu@ip-xxxxx:~/dill$
    ```
    
7. Import your keys to your keystore:
    
    ```
   ./dill-node accounts import --andes --wallet-dir ./keystore --keys-dir validator_keys/ --accept-terms-of-use
    ```
    
    During this process, you need to configure and save your keystore password.
    
    sample output
    
    ```bash
    
    ubuntu@ip-xxxxx:~/dill$ ./dill-node accounts import --andes --wallet-dir ./keystore --keys-dir validator_keys/ --accept-terms-of-use
    [2024-07-13 08:08:34]  INFO flags: Running on the Andes Beacon Chain Testnet
    Password requirements: at least 8 characters
    New wallet password:
    Confirm password:
    [2024-07-13 08:08:39]  INFO wallet: Successfully created new wallet walletPath=/home/ubuntu/dill/keystore
    [2024-07-13 08:08:39]  WARN client: You are using an insecure gRPC connection. If you are running your beacon node and validator on the same machines, you can ignore this message. If you want to know how to enable secure connections, see: https://docs.prylabs.network/docs/prysm-usage/secure-grpc
    [2024-07-13 08:08:39]  INFO accounts: importing validator keystores...
    [2024-07-13 08:08:39]  INFO accounts: checking directory for keystores: /home/ubuntu/dill/validator_keys
    Enter the password for your imported accounts:
    Importing accounts, this may take a while...
    Importing accounts... 100% [===================================================================================]  [1s:0s]
    [2024-07-13 08:08:44]  INFO local-keymanager: Reloaded validator keys into keymanager
    [2024-07-13 08:08:44]  INFO local-keymanager: Successfully imported validator key(s) pubkeys=0xxxxx
    [2024-07-13 08:08:44]  INFO accounts: Imported accounts [xxxxxx], view all of them by running `accounts list`
    ubuntu@ip-xxxxx:~/dill$
    ```
    
9. Write the `password` you configured in the previous step into a file:
    
    ```
   echo <my-password> > walletPw.txt
    ```
    
11. Start the light validator node
    
    Here you need to specify `your password` file as a startup parameter. You can start the program with the following command:
    
    ```
    ./start_light.sh -p <password-path>
    ``` 
    
    sample output
    
    ```bash
    ubuntu@xxxxx:~/dill$ ./start_light.sh -p walletPw.txt
    Option --pwdfile, argument 'walletPw.txt'
    Remaining arguments:
    using password file at walletPw.txt
    start light node
    start light node done
    ubuntu@xxxxx:~/dill$ nohup: redirecting stderr to stdout
    
    ```
    
13. to check if the node is up and running and there should be one dill process running:
    ```
    ps -ef | grep dill
    ```

sample output

```bash
ubuntu      1981       1 86 08:09 pts/0    00:00:43 /home/ubuntu/dill/dill-node --light --embedded-geth --datadir /home/ubuntu/dill/light_node/data/beacondata --genesis-state /home/ubuntu/dill/genesis.ssz --grpc-gateway-host 0.0.0.0 --initial-validators /home/ubuntu/dill/validators.j
...
```

## Staking

- visit https://staking.dill.xyz/
- upload `deposit_data-*.json`
    - you can use `scp` to copy to local or simply `ubuntu@ip-xxx:~/dill$ cat ./validator_keys/deposit_data-xxxx.json` to get the content and make a copy in your local machine

![image](https://github.com/user-attachments/assets/ed1deead-94a7-4385-85ef-829d1c949791)


- Connect to MetaMask，make sure you have enough balance（>2500 DILL）

![image](https://github.com/user-attachments/assets/c3eddf8a-23b5-4b98-a4ae-c947803d1dc3)


- Send deposit，using MetaMask to send a deposit transaction

  ![image](https://github.com/user-attachments/assets/fc5b37db-d667-4b12-a227-e685747c3c19)
