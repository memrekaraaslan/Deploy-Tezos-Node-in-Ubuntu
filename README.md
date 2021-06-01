# tezos-install.sh
One click installation script for Tezos Deployment

#!/bin/bash

###################################################################
#Script Name	:This is one-click-easy-installation script.

#Description	:#This script completes the process of deploying Tezos Node on an Ubuntu device.
                 #If you see the message "Node is bootstrapped" when the transaction is complete,
                 # your Tezos node is synchronized with the blockchain and you can now transact on the blockchain.

#Author       	:Emre Karaaslan

#Email         	:memrekaraaslan@gmail.com
###################################################################


#Update and upgrade to packages.
sudo apt update -y
sudo apt upgrade -y

#install the necessary packages.
sudo apt install -y curl rsync cargo debianutils libffi-dev perl pkg-config git m4 build-essential patch unzip bubblewrap wget libev-dev libgmp-dev pkg-config libhidapi-dev opam


#Installing the OPAM and completing the necessary configurations
sh <(curl -sLo /usr/bin/ https://raw.githubusercontent.com/ocaml/opam/master/shell/install.sh)
no &> /dev/null | opam init --bare
wget -O latest-release:version.sh https://gitlab.com/tezos/tezos/raw/latest-release/scripts/version.sh
source latest-release:version.sh
opam switch create for_tezos $ocaml_version  &> /dev/null
eval $(opam env)
opam install -y depext
opam depext tezos

#Install Tezos Node with OPAM
opam install -y tezos

#Tezos binaries require the Zcash parameter files to run. Wget and run the following script for install Zcash Parameters.
FILE=~/fetch-params.sh

if test -f "$FILE"; then
    rm -rf $FILE
fi

wget https://raw.githubusercontent.com/zcash/zcash/master/zcutil/fetch-params.sh
chmod a+x fetch-params.sh
./fetch-params.sh
eval $(opam env)

#Generate a new identity for the node. Identity file stored in ~/.tezos-node/identity.json
tezos-node identity generate &> /dev/null
echo "Identity file stored in ~/.tezos-node/identity.json"


#We have to import a snapshot.Importing a snapshot is like going to a particular time-stamp in the blockchain,
# and then syncing up with the blockchain from that time.
rm -rf ~/snapshot*
wget https://snapshots-tezos.giganode.io/snapshot-mainnet_29-05-2021-0134_1491800_BKocnMqCsjDZxNT2w3vVvWnqT6qrfAFfTjbKnpXCGsGVVHNgKMg.rolling
tezos-node snapshot import snapshot* &> /dev/null

#Allows your node to sync up with the blockchain by reaching the latest block in the chain
tezos-node run --private-mode --rpc-addr 127.0.0.1:8732 --connections 5 >> ~/.tezos-node.log 2>&1 &

echo "Waiting for boostrap..."
echo "When you see the message Bootstrapped, your tezos node is synced with the blockchain and you may now perform operations on the blockchain."
tezos-client bootstrapped

#View block hash:   This is stored in ~/.myBlockHash.txt
tezos-client rpc get /chains/main/blocks/head > ~/.myBlockHash.json &> /dev/null
