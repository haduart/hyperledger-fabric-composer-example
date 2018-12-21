# Testing out Hyperledger Fabric 1.12 & Composer setup

## Setup hyperledger-fabric on mac
* [Development environment](https://hyperledger-fabric.readthedocs.io/en/release-1.3/dev-setup/devenv.html)
* [Fabric Dev Servers setup](https://github.com/hyperledger/composer-tools/blob/master/packages/fabric-dev-servers/README.md)

```commandline
$ curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.zip
$ unzip fabric-dev-servers.zip
$ export FABRIC_VERSION=hlfv12
$ ./downloadFabric.sh
$ ./startFabric.sh
```

Using this commands you will download and start Hyperledger Fabric 1.12.
Inside the fabric tools, you can go to the subfolder where the docker-compose file is, in that way is easier to see and control the docker containers:

```commandline
$ cd fabric-scripts/hlfv11/composer/
$ docker-compose logs -f
$ docker-compose ps
$ docker-compose stop
$ docker-compose start
$ docker-compose pause
$ docker-compose unpause
```

## Analyzing Docker Containers

### Peer Node
```commandline
$ docker exec -it 2770c86d71cf /bin/bash
$ peer node status
$ peer channel list
```

Commands: 
* chaincode: Operate a chaincode: install, instantiate, invoke, package, query, signpackage, upgrade
* channel: Operate a channel: create, fetch, join, list, update
* logging: Log levels: getlevel, setlevel, revertlevels
* node: Operate a peer node: start, status
* version: Print fabric peer version


### Orderer 
```commandline
$ docker exec -it 6f2164d39d96 /bin/bash
$ orderer version
$ orderer --help
```

### Certification Authority (CA)
```commandline
$ docker exec -it 1e694d90d83f /bin/bash
$ fabric-ca-server --help
$ fabric-ca-client --help
```

### CouchDB Node 

[http://192.168.99.100:5984/](http://192.168.99.100:5984/) or [http://localhost:5984/](http://localhost:5984/)

## Using the hyperledger-composer yo generator
```commandline
$ npm install -g yo
$ npm install -g generator-hyperledger-composer
$ yo hyperledger-composer
```

It can generate Angular example apps, Business Networks, LoopBack (an open API) and the Model.

## Installing Hyperledger Composer CLI
```commandline
$ npm install -g composer-cli
$ composer network deploy -a ./airlinev8@0.0.1.bna -c PeerAdmin@hlfv1 -A admin -S adminpwd
```

### Participant Roles
Participants roles are under /Users/haduart/.composer 
```commandline
$ composer card --help
$ composer card list
$ composer card export --file test.card --card PeerAdmin@hlfv1
$ composer card delete --card  PeerAdmin@hlfv1
```

To create a new card, for example for the Admin (it’s the first one that you should create) you have to use the createPeerAdminCard script.
```commandline
$ cd /Users/haduart/fabric-tools/fabric-scripts/hlfv11
$ ./createPeerAdminCard.sh
$ ./createPeerAdminCard.sh -h 192.168.99.100
```

Cards used by tools & SDK are managed on the file system and basically, there’s the connection.json and the credentials folder. 

## Setting up Composer Playground locally
[Development tools](https://hyperledger.github.io/composer/latest/installing/development-tools.html)
```commandline
$ xcode-select --install
$ npm install node-gyp
$ npm install -g composer-rest-server
$ npm install -g generator-hyperledger-composer
$ npm install -g composer-playground
$ composer-playground
```

## Deploying a Network Application
First, we need to create a business network model
```commandline
$ yo hyperledger-composer
```
We will create a dist folder and create our first distribution:
```commandline
$ composer archive create --sourceType dir --sourceName . -a ./dist/test-bna.bna
$ composer network install -a dist/test-bna.bna -c PeerAdmin@hlfv1
$ composer network start --networkName test-bna --networkVersion 0.0.3 --networkAdmin admin --networkAdminEnrollSecret adminpw --card PeerAdmin@hlfv1 --file test.card
```

After starting the BN there will be a file created. In our case it’s named test.card.

Then we should import the new Network Administrator’s card:
```commandline
$ composer card import -f test.card
```
After importing the network card we can check its availability with the ping command:
```commandline
$ composer network ping -c admin@test-bna
```
And check everything that has been pushed as a network with the network list command:
```commandline
$ composer network list -c admin@test-bna
```

## Upgrade the network to a new version
We build and install a new version into the network:
```commandline
$ npm install
$ composer network install -a dist/test-bna.bna -c PeerAdmin@hlfv1
```
Then we upgrade to the new version:
```commandline
$ composer network upgrade -c PeerAdmin@hlfv1 -n test-bna -V 0.0.4
$ composer network ping -c admin@test-bna
```
## Starting Composer Rest Server
With the Rest server you can do CRUD operations and invoke transactions. 
```commandline   
$ composer-rest-server -c admin@test-bna -p 3000 --explorer
```
### Starting the REST server with multiple user mode enabled
[Enabling multiuser](https://hyperledger.github.io/composer/v0.16/integrating/enabling-multiuser)

```commandline
$  composer-rest-server -c admin@test-bna -p 3000 --explorer -m true
```
Enabling HTTPS and TLS by using the sample certificate and private key pair
```commandline
$ composer-rest-server -c admin@test-bna -p 3000 --explorer -m true -t
$ composer-rest-server -c admin@test-bna -p 3000 --explorer -m true -t -e /tmp/cert.pem -k /tmp/key.pem
```

## Creating an Angular app against the composer rest server
```commandline
$ yo hyperledger-composer:angular

$ cd angular-test/
$ npm install
$ npm test
$ npm start

```

## Check these projects!
* [Serverless Hyperledger](https://github.com/mataide/serverless-hyperledger#readme)
* [Amazon S3 implementation of hyperledger composer wallet](https://github.com/Pop-Code/composer-wallet-s3#readme)
* [How to setup and build Hyperledger Fabric Blockchain Applications (EC2)](https://domsteil.com/2017/04/22/how-to-setup-hyperledger-fabric-v1-0-on-aws/)
* [Chaincode APIs](https://openblockchain.readthedocs.io/en/latest/API/ChaincodeAPI/) 
* [Core APIs](https://openblockchain.readthedocs.io/en/latest/API/CoreAPI/)
 


