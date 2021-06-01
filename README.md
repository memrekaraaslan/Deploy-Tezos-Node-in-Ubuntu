# tezos-install.sh
One click installation script for Tezos Deployment

With the script I wrote, an XTZ full node is deployed on an empty Ubuntu machine:
* I preferred the OPAM package manager as the installation type.
* After installing Tezos with OPAM, Zcash Parameters were set.
* Then the credentials for tezos-client are generated and stored in ~/.tezos-node/identity.json.
* A ready snapshot has been imported.
* Tezos node reached the last block in the chain and synchronized with the blockchain.
* When the script is completed, our Tezos Node will be synchronized with the block chain. For this, you should see the message "Node is bootstrapped".

* When the script is completed, we can see our own blockchain data in the ~/.node-tezos/ directory. You can also check the ~/.tezos-node.log file to review the deployment logs.
