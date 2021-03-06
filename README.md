## *** work in progress *** 
# Create and Deploy a Blockchain Network using Hyperledger Fabric SDK Java

Blockchain is a shared, immutable ledger for recording the history of transactions. The Linux Foundation’s Hyperledger Fabric, the software implementation of blockchain IBM is committed to, is a permissioned network. Hyperledger Fabric is a platform for distributed ledger solutions underpinned by a modular architecture delivering high degrees of confidentiality, resiliency, flexibility and scalability. 

In a blockchain application, blockchain network works as a back-end and application has an front-end to communicate with the network. To set up the communication between front-end and back-end, Hyperledger Fabric community offers a number of SDKs for a wide variety of programming languages like the Node.js SDK and Java SDK. This code pattern explains the methodology to create, deploy and test the blockchain network using hyperledger fabric sdk java. 

It would be helpful for the Java developers, who started to look into Hyperledger Fabric platform and would like to use fabric sdk java for their projects. The SDK helps facilitate Java applications to manage the lifecycle of Hyperledger channels and user chaincode. The SDK also provides a means to execute user chaincode, query blocks and transactions on the channel, and monitor events on the channel. This code pattern will help to get the process started to build a Hyperledger Fabric Java application.

When the reader has completed this pattern, they will understand how to create, deploy and test a blockchain network using Hyperledger Fabric SDK Java. This pattern will provision a Hyperledger Fabric network consisting of two organizations, each maintaining two peer node, two certificate authorities (ca) for each organization and a solo ordering service. Then it will demonstrate to create and initialize channel, install and instantiate chain code and perform invoke and query on the blockchain network.

## Flow

   ![](images/architecture.png)

1. Generate the artifacts using cryptogen and configtx for peers and channel in network. Currently these are already generated and provided in the code repository to use as-is.
2. Build the network using docker-compose and the generated artifacts.
3. Use Fabric Java SDK APIs to work with and manage the network. 
    * create and initialize the channel
    * install and instantiate the chaincode
    * perform invoke and query to test the network


## Included Components

* [Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/): Hyperledger Fabric is a platform for distributed ledger solutions underpinned by a modular architecture delivering high degrees of confidentiality, resiliency, flexibility and scalability.

* [Docker](https://www.docker.com/): Docker is an open platform for developers and sysadmins to build, ship, and run distributed applications.

* [Hyperledger Fabric Java SDK](https://github.com/hyperledger/fabric-sdk-java)

## Featured Technologies

* [Blockchain](https://en.wikipedia.org/wiki/Blockchain): A blockchain is a digitized, decentralized, public ledger of all transactions in a network.

* [Java](https://en.wikipedia.org/wiki/Java_(programming_language)): Java is a general-purpose computer-programming language that is concurrent, class-based and object-oriented.

## Watch the Video
TODO

## Pre-requisites

* [Docker](https://www.docker.com/get-docker) - v1.13 or higher
* [Docker Compose](https://docs.docker.com/compose/overview/) - v1.8 or higher
* [Git Client](https://git-scm.com/downloads) - needed for clone commands

## Steps

Follow these steps to setup and run this code pattern. 

1. [Setup the Blockchain Network](#1-setup-the-blockchain-network)
2. [Build the client based on Fabric Java SDK](#2-build-the-client-based-on-fabric-java-sdk)
3. [Create and Initialize the channel](#3-create-and-initialize-the-channel)
4. [Deploy and Instantiate the chaincode](#4-deploy-and-instantiate-the-chaincode)
5. [Perform Invoke and Query on network](#5-perform-invoke-and-query-on-network)

### 1. Setup the Blockchain Network

[Clone this repo](https://github.com/IBM/blockchain-application-using-fabric-java-sdk) using the following command.

```
$ git clone https://github.com/IBM/blockchain-application-using-fabric-java-sdk.git
```

To build the blockchain network, the first step is to generate artifacts for peers and channels using cryptogen and configtx. The utilities used and steps to generate artifacts are explained [here](http://hyperledger-fabric.readthedocs.io/en/release-1.0/build_network.html). In this pattern all required artifacts for the peers and channel of the network are already generated and provided to use as-is. Artifacts can be located at:

   ```
   network_resources/crypto-config
   network_resources/config
   ````

The automated scripts are provided to build the network under `network` directory. The `network/docker-compose.yaml` file defines the blockchain network topology. This pattern provisions a Hyperledger Fabric network consisting of two organizations, each maintaining two peer node, two certificate authorities for each organization and a solo ordering service. Need to run the scripts as follows to build the network.

   ```
   cd network
   chmod +x build.sh
   ./build.sh
   ```

To stop the running network, run the following script.

   ```
   cd network
   chmod +x stop.sh
   ./stop.sh
   ```

To delete the network completely, following script need to execute.

   ```
   cd network
   chmod +x teardown.sh
   ./teardown.sh
   ```

### 2. Build the client based on Fabric Java SDK

The previous step creates all required docker images with the required configuration. To work with this network using hyperledger fabric SDK java, perform the following steps.

* The java client sources are present in the folder `java` of the repo.
* Open a command terminal and navigate to the `java` directory in the repo. Run the command `mvn install`.

   ```
   cd java
   mvn install
   ```

A jar file `blockchain-java-sdk-0.0.1-SNAPSHOT-jar-with-dependencies.jar` is built and can be found under the `target` folder. This jar can be renamed to `blockchain-client.jar` to keep the name short. 

   ```
   cd target
   cp blockchain-java-sdk-0.0.1-SNAPSHOT-jar-with-dependencies.jar blockchain-client.jar
   ```
   
Copy this built jar into network_resources directory. This is required as the java code can access required artifacts during execution.

   ```
   cp blockchain-client.jar ../../network_resources
   ```

### 3. Create and Initialize the channel

In this code pattern, we create one channel `mychannel` which is joined by all four peers. To create and initialize the channel, run the following command.

   ```
   cd ../../network_resources
   java -cp blockchain-client.jar org.app.network.CreateChannel
   ```

Output:

   ```Apr 20, 2018 5:11:42 PM org.app.util.Util deleteDirectory
      INFO: Deleting - users
      Apr 20, 2018 5:11:45 PM org.app.network.CreateChannel main
      INFO: Channel created mychannel
      Apr 20, 2018 5:11:45 PM org.app.network.CreateChannel main
      INFO: peer0.org1.example.com at grpc://localhost:7051
      Apr 20, 2018 5:11:45 PM org.app.network.CreateChannel main
      INFO: peer1.org1.example.com at grpc://localhost:7056
      Apr 20, 2018 5:11:45 PM org.app.network.CreateChannel main
      INFO: peer0.org2.example.com at grpc://localhost:8051
      Apr 20, 2018 5:11:45 PM org.app.network.CreateChannel main
      INFO: peer1.org2.example.com at grpc://localhost:8056
   ```  
   
### 4. Deploy and Instantiate the chaincode

This code pattern uses a sample chaincode `fabcar` to demo the usage of Hyperledger Fabric SDK Java APIs. To deploy and instantiate the chaincode, execute the following command.

   ```
   java -cp blockchain-client.jar org.app.network.DeployInstantiateChaincode
   ```
   
   Output:
   
   ```Apr 20, 2018 5:12:00 PM org.app.client.FabricClient deployChainCode
      INFO: Deploying chaincode fabcar using Fabric client Org1MSP admin
      Apr 20, 2018 5:12:01 PM org.app.client.FabricClient deployChainCode
      INFO: Deploying chaincode fabcar using Fabric client Org2MSP admin
      Apr 20, 2018 5:12:01 PM org.app.client.ChannelClient instantiateChainCode
      INFO: Instantiate proposal request fabcar on channel mychannel with Fabric client Org2MSP admin
      Apr 20, 2018 5:12:01 PM org.app.client.ChannelClient instantiateChainCode
      INFO: Instantiating Chaincode ID fabcar on channel mychannel
      Apr 20, 2018 5:12:44 PM org.app.client.ChannelClient instantiateChainCode
      INFO: Chaincode fabcar on channel mychannel instantiation java.util.concurrent.CompletableFuture@4d0d9fe7[Not completed]
   ```
   
   > **Note:** The chaincode fabcar.go was taken from the fabric samples available at - https://github.com/hyperledger/fabric-samples/tree/release-1.1/chaincode/fabcar/go.

### 5. Perform Invoke and Query on network

Blockchain network has been setup completely and is ready to use. Now we can test the network by performing invoke and query on the network. The `fabcar` chaincode allows us to create a new asset which is a car. For test purpose, invoke operation is performed to create a new asset in the network and query operation is performed to list the assets of the network. Perform the following steps to check the same.

   ```
   java -cp blockchain-client.jar org.app.chaincode.invocation.InvokeChaincode
   ```

   Output:
   
   ```Apr 20, 2018 5:13:03 PM org.app.client.CAClient enrollAdminUser
     INFO: CA -http://localhost:7054 Enrolled Admin.
     Apr 20, 2018 5:13:04 PM org.app.client.ChannelClient sendTransactionProposal
     INFO: Sending transaction proposal on channel mychannel
     Apr 20, 2018 5:13:04 PM org.app.client.ChannelClient sendTransactionProposal
     INFO: Transaction proposal on channel mychannel OK SUCCESS with transaction       
     id:a298b9e27bdb0b6ca18b19f9c78a5371fb4d9b8dd199927baf37379537ca0d0f
     Apr 20, 2018 5:13:04 PM org.app.client.ChannelClient sendTransactionProposal
     INFO: 
     Apr 20, 2018 5:13:04 PM org.app.client.ChannelClient sendTransactionProposal
     INFO: java.util.concurrent.CompletableFuture@22f31dec[Not completed]
     Apr 20, 2018 5:13:04 PM org.app.chaincode.invocation.InvokeChaincode main
     INFO: Invoked createCar on fabcar. Status - SUCCESS
  ```   

   ```
   java -cp blockchain-client.jar org.app.chaincode.invocation.QueryChaincode
   ```
   
   Output:
   
   ```Apr 20, 2018 5:13:28 PM org.app.client.CAClient enrollAdminUser
      INFO: CA -http://localhost:7054 Enrolled Admin.
      Apr 20, 2018 5:13:29 PM org.app.chaincode.invocation.QueryChaincode main
      INFO: Querying for all cars ...
      Apr 20, 2018 5:13:29 PM org.app.client.ChannelClient queryByChainCode
      INFO: Querying queryAllCars on channel mychannel
      Apr 20, 2018 5:13:29 PM org.app.chaincode.invocation.QueryChaincode main
      INFO: [{"Key":"CAR1", "Record":{"make":"Chevy","model":"Volt","colour":"Red","owner":"Nick"}}]
      Apr 20, 2018 5:13:39 PM org.app.chaincode.invocation.QueryChaincode main
      INFO: Querying for a car - [Ljava.lang.String;@77d2e85
      Apr 20, 2018 5:13:39 PM org.app.client.ChannelClient queryByChainCode
      INFO: Querying queryCar on channel mychannel
      Apr 20, 2018 5:13:39 PM org.app.chaincode.invocation.QueryChaincode main
      INFO: {"make":"Chevy","model":"Volt","colour":"Red","owner":"Nick"}
   ```

## Troubleshooting

[See DEBUGGING.md.](DEBUGGING.md)

## License
[Apache 2.0](LICENSE)

