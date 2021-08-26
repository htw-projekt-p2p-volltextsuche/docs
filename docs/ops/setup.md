# Setup
In order to setup a network of peers you need to setup one "data" node as well as as many peers you desire. If you don't need the scaling capability, you can just setup one data node as well.

## Development
### 1. Setup your firewall to allow communication through these ports:
- 22 (ssh)
- 80 (http)
- 8080 (alternate http port)
- 8421 (fulltext-search)
- 8090, 8070 (p2p-network)
- 8430 (mongodb)
- 8081 (ui-backend)

### 2. Install docker: https://docs.docker.com/get-docker/
### 3. Install docker-compose: https://docs.docker.com/compose/install/
### 4. clone this repository
### 5. go into ops/data/
### 6. create a .env file with the following content
```
MONGO_INITDB_ROOT_USERNAME=<[choose one]>  
MONGO_INITDB_ROOT_USERNAME=<[choose one]>  
DATA_HOST=<[the ip of your pc]>
INDEX_STORAGE_POLICY=local
UI_PORT=80
```
### 8. start the node
- you can start it with ```docker-compose up``` to see the complete log in the terminal.
- you can start it with ```docker-compose up -d``` if you want to run it as a deamon. 
### 9. Stop the nodes again with ```docker-compose down``` if you are done.

## Production
The first four steps are documented for transparancy reasons. You can skip them by cloneing this repository and executing install.sh The same goes for a testing-environment on which you don't need to setup the firewall and may already have installed docker (possible with root permissions).

***Setup without scripts.***
### 1. Setup your firewall to allow communication through olny these ports:
- 22 (ssh)
- 80 (http)
- 8080 (alternate http port)
- 8421 (fulltext-search)
- 8090, 8070 (p2p-network)
- 8430 (mongodb)
- 8081 (ui-backend)

Route port 80 to port 8080 to enable rootless access of the docker-container port

### 2. install docker rootless: https://docs.docker.com/engine/security/rootless/
### 3. install docker-compose: https://docs.docker.com/compose/install/
### 4. clone this repository
### 5. go into the folder representing this repository
### 6. create a .env file in the folder representing the type of node you want to deploy (peer or data) with the following values

```env
MONGO_INITDB_ROOT_USERNAME=<[for data choose one/for peer use the name of data]>  
MONGO_INITDB_ROOT_USERNAME=<[for data choose one/for peer use the password of data]>  
DATA_HOST=<[the ip of the server running the data node]>
INDEX_STORAGE_POLICY=<[local, distributed or lazy-distributed]>
PEER_LIST=<[see step 10 or leave blank for a single node setup]>
UI_PORT=8080
```
[Read more about the index storage policies](https://github.com/htw-projekt-p2p-volltextsuche/fulltext-search#index-storage-policies)
  
### 7. go into the folder representing the type of node you want to deploy (peer or data)
### 8. start the nodes
- you can start them with ```docker-compose up``` to see the complete log in the terminal.
- you can start them with ```docker-compose up -d``` if you want to run it as a deamon. 
### 9. Stop the nodes again with ```docker-compose down``` after seeing the ascii-art "https"-logo.
### 10. Setup the PEER_LIST (after running all instances at least once)(only necessary for multi-node setups)
- copy the output of all the p2pframework containers signaling the "own multiaddr:" into the "PEER_LIST" var of every node. They should only be sepereated by a comma. [See](https://github.com/htw-projekt-p2p-volltextsuche/p2p-dht#peer_list)
- replace the "0.0.0.0"-part of the adresse with the corresponding ip of the node
### 11. Start the nodes again with ```docker-compose up```.