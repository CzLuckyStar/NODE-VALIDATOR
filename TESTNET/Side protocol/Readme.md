
# Side-Protocol (Testnet3)

Update system

    sudo apt update
    sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y

Install Go

    rm -rf $HOME/go
    sudo rm -rf /usr/local/go
    cd $HOME
    curl https://dl.google.com/go/go1.20.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
    cat <<'EOF' >>$HOME/.profile
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/go
    export GO111MODULE=on
    export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
    EOF
    source $HOME/.profile
    go version

Install Node

    cd $HOME
    rm -rf sidechain
    git clone -b dev https://github.com/sideprotocol/sidechain.git
    cd sidechain
    git checkout v0.7.0
    make install
    sided version

Initialize Node: Replace NodeName with your own moniker.

    sided init NodeName --chain-id=side-testnet-3

Download Genesis

    curl -Ls https://ss-t.side.nodestake.org/genesis.json > $HOME/.side/config/genesis.json

Download addrbook

    curl -Ls https://ss-t.side.nodestake.org/addrbook.json > $HOME/.side/config/addrbook.json

Create Service

    sudo tee /etc/systemd/system/sided.service > /dev/null <<EOF
    [Unit]
    Description=sided Daemon
    After=network-online.target
    [Service]
    User=$USER
    ExecStart=$(which sided) start
    Restart=always
    RestartSec=3
    LimitNOFILE=65535
    [Install]
    WantedBy=multi-user.target
    EOF
    sudo systemctl daemon-reload
    sudo systemctl enable sided

Download Snapshot

    SNAP_NAME=$(curl -s https://ss-t.side.nodestake.org/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
    curl -o - -L https://ss-t.side.nodestake.top/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.side

Launch Node

    sudo systemctl restart sided
    journalctl -u sided -f

Check syn: False

    sided status 2>&1 | jq .SyncInfo.catching_up

Create New Wallet

    sided keys add wallet

Query Wallet Balance

    sided q bank balances $(sided keys show wallet -a)

 Create new Validator: Change your custom information at: Your_moniker, your_id_keybase, your_info. What items don't need to be able to be deleted

     sided tx staking create-validator \
     --amount=1000000uside \
     --pubkey=$(sided tendermint show-validator) \
     --moniker="Your_moniker" \
     --identity=your_id_keybase  \
     --details="your_info" \
     --chain-id="side-testnet-3" \
     --commission-rate="0.10" \
     --commission-max-rate="0.20" \
     --commission-max-change-rate="0.01" \
     --min-self-delegation="1" \
     --fees="200uside" \
     --from=wallet

 Edit Existing Validator:  Change your custom information.

     sided tx staking edit-validator \
        --new-moniker="New_moniker" \
        --identity=new_identity \
        --details="new_info" \
        --website="your_website" \
        --chain-id=side-testnet-3 \
        --from=wallet \
        --gas-prices=0.5uside \
        --gas-adjustment=1.5 \
        --gas=auto \
        -y
Delegate to your validator

    sided tx staking delegate $(sided keys show wallet --bech val -a) 1000000uside --from wallet --chain-id side-testnet-3 --gas-prices 0.5uside --gas-adjustment 1.5 --gas auto -y

Unjail

    sided tx slashing unjail --from wallet--chain-id side-testnet-2 --gas auto --fees 1000uside -y

Delete node

        sudo systemctl stop sided && sudo systemctl disable sided && sudo rm /etc/systemd/system/sided.service && sudo systemctl daemon-reload && rm -rf $HOME/.side && rm -rf $HOME/.sidechain && rm -rf sidechain && rm -rf rebus.core && sudo rm -rf $(which sided)

Send Token

        sided tx bank send wallet <Receive_wallet> <Amout>uside --chain-id side-testnet-3 --gas-prices 0.5uside --gas-adjustment 1.5 --gas auto -y

                
            
    
    
