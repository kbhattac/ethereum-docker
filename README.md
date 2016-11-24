# Ethereum Docker

Get started creating Ethereum development and test single and multi-node clusters
rapidly using Docker. Please note that this project is meant for playing with a private Ethereum network, 
and doesn't follow best practices for network access & security for production workloads.


We provide full Ethereum test nodes (using the [Ethereum Go client](https://github.com/ethereum/go-ethereum) with all APIs enabled by default as well as a monitoring dashboard provided via [Netstats](https://github.com/cubedro/eth-netstats).

## Getting started

### Local development with two nodes en netstats dashboard 

```
docker-compose up -d
```

By default this will create:

* 1 Ethereum bootstrapped container
* 1 Ethereum container (which connects to the bootstrapped container on launch)
* 1 Netstats container (with a Web UI to view activity in the cluster)

To access the Netstats dashboard

```
open http://localhost:3000/
```

Note that you need to update the ```docker-compose.yml``` to use another port (i.e. 80) for exposing the Netstats dashboard.

##### Scaling the number of nodes/containers in the cluster

```
docker-compose scale eth=3
```

Will scale the number of Ethereum nodes upwards (replace 3 with however many nodes
you prefer). These nodes will connect to the P2P network (via the bootstrap node)
by default.

### Running it on Azure

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fkbhattmsft%2Fethereum-docker%2Fmaster%2Fazure%2Fazuredeploy.json)
[![Visualize](http://armviz.io/visualizebutton.png)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fmeken%2Fethereum-docker%2Fmaster%2Fazure%2Fazuredeploy.json)

This basically provisions an Ubuntu VM on Azure with Docker installed and the abovementioned containers running. 
You can then view the Netstats dashboard through the VM's DNS name, and/or connect to the Ethereum nodes through 
RPC to access the console. 
You can also just ssh into the VM to get access to the full environment and scale up/down the number of nodes. 

#### Test accounts ready for use

As part of the bootstrapping process we bootstrap 10 Ethereum accounts for use
pre-filled with 20 Ether for use in transactions by default.

If you want to change the amount of Ether for those accounts
See ```files/genesis.json```.

#### Interact with geth

If you want to start mining or stop mining you need to connect to the node via:
```
docker exec -it ethereumdocker_eth_1 geth attach ipc://root/.ethereum/devchain/geth.ipc
```
Replace ethereumdocker_eth_1 with the container name you wish to connect to.

Once you're connected to the geth console, you can then run the following command to start mining:
```
> miner.start(1)
```
This will run the miner process with 1 thread. 
The mining rewards will be put in the account for the coinbase, which is the first account configured 
in the genesis file. 

After that you can start sending transactions:
```
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x....
Passphrase: <empty passphrase for all pre-configured accounts>
> eth.sendTransaction({from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(5, "ether")}) 
```
You could monitor the mining process and the pending transactions through the Netstats dashboard.

You can also consider using the [MetaMask](https://metamask.io/) browser plugin to manage accounts and transactions.
The plugin also works nicely with online smart contract compilers for deploying your own contracts.

