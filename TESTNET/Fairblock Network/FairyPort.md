# Fairyport
`Fairyport` is an off-chain service for sending aggregated keyshares to destination chains from `fairyring`. This tutorial explains how to setup and run `fairyport`. Ensure you have followed the prerequisites and installed the `fairyring` binary prior to following the instructions below.

### Install fairyport
Cloning the repository:
```
cd $HOME
git clone https://github.com/Fairblock/fairyport.git
cd fairyport
```
Install fairyport binary:
```
go mod tidy
go install
```
Setup fairyport
Initialize fairyport config:
```
fairyport init
```
`init` command create a default config `config.yml` in the default directory `$HOME/.fairyport` . The config looks like this:
```
FairyRingNode:
    grpcport: 9090
    ip: 127.0.0.1
    port: 26657
    protocol: rpc
DestinationNode:
    grpcport: 9090
    ip: 127.0.0.1
    port: 26657
    protocol: rpc
Mnemonic: '# mnemonic'
```
### Detailed explanation on the config:

**FairyRingNode**

Option:	  Description

ip:	      The IP address that Fairy Ring Node will use.

port:	    The port that Fairy Ring Node will use for TendermintRPC

protocol:	The protocol used for communication via the TendermintRPC

grpcport:	The port that Fairy Ring Node will use for gRPC communication with other nodes.

**DestinationNode**

Option:	  Description

ip:	      The IP address that Destination Node will use.

port:	    The port that Destination Node will use for TendermintRPC

protocol:	The protocol used for communication via the TendermintRPC

grpcport:	The port that Destination Node will use for gRPC communication with other nodes.

**Mnemonic**

Option:    	Description

Mnemonic	  The seed phrase used to generate the private key for the account responsible for making transactions to the Destination Chain

Update the node ip and ports for `fairyport` to connect to and `Mnemonic` to your mnemonic phase. Note that fairyport derives your address with path `m/44'/118'/0'/0/0` by default.

The updated config should looks like this:
```
FairyRingNode:
  ip: "127.0.0.1"
  port: 26657
  protocol: "tcp"
  grpcport: 9090

DestinationNode:
  ip: "127.0.0.1"
  port: 26659
  protocol: "tcp"
  grpcport: 9092

Mnemonic: "banana unusual correct orange dwarf fortune tennis sell primary giggle canal ask fish movie loud elite region glory session wonder frozen clap mountain barrel"
```

### Start fairyport
After you setup fairyport, you can start running fairyport by:
```
fairyport start --config $HOME/.fairyport/config.yml
```
