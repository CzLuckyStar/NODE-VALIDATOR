# Farcaster Node

### Server Details : 

16 GB RAM

4 CPU

Min 140 GB Disk Space 

Ubuntu 22.04

Contabo VPS 2 : 
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/aae01e29-1428-4721-86b8-90df771d41d7)


### Api Service : 

Alchemy : `alchemy.com`
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/5e7375dd-188d-484d-8a3a-520b4bbc6638)


Required APIs : 
Ethereum Mainnet RPC URL 
Optimism L2 Mainnet RPC URL 

### Farcaster ID : 
Farcaster Sing Up : `https://warpcast.com/~/invite-page/549111?id=d16e715a`
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/ca7f1ee8-df21-435f-a5a4-9f3916c4f77d)



### Update : 
```
sudo apt-get update && sudo apt-get upgrade -y
```
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/0c48b59a-d88a-4a71-8903-51d6f5217369)

### Install Screen  : 
```
apt-get install screen -y
```
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/d98cff91-93d7-48f0-acc7-b3ce1c457ce0)

### Screen : 
```
screen -S farcaster
```

### Deploy Your Node : 
```
curl -sSL https://download.thehubble.xyz/bootstrap.sh | bash
```
It will download some files and ask you for the necessary information: RPC RPC `Farcaster ID`:
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/83952570-7468-4466-ab3f-1683675a0500)


## SYNC :
It will then download the snapshot to sync : 
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/6da956ef-bbb2-402d-8d31-a6fe3581f04e)


Sucsess Logs : 

![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/3ad244fb-e20a-4887-be25-b32a55b76914)

## Grafana : 
`http://your-server-ip:3000`
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/d23c35ea-71be-4ea5-8307-4fa5ccd1f0ea)


### SYNC Status : 
_Message SYNC %100_
![image](https://github.com/CzLuckyStar/NODE-VALIDATOR/assets/130622293/0a4b15cf-5db8-4074-9b20-88a7816f3b73)


## Other Commands : 
```
cd hubble
```
### 🌫️Logs : 

```
./hubble.sh logs
```

### 🧧Stop : 

```
./hubble.sh down
```

### 💫Restart :

```
./hubble.sh up
```

