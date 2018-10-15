## Add A Peer to Organization(Single machine)

Example：Make third peer, `peer2`, add to `mychannel` in `organization1`

```sh
cd ./network
```

Edit `crypto-config.yml` , change `PeerOrgs.Template.Count` to 3
```yml
OrdererOrgs:
  - Name: Orderer
    Domain: example.com
    Specs:
      - Hostname: orderer
PeerOrgs:
  - Name: Org1
    Domain: org1.example.com
    Template:
      Count: 3 # <--
    Users:
      Count: 1
```

Create MSP of `peer2`
```sh
../tools/cryptogen extend --config=./crypto-config.yaml
```

Create docker-compose file for `peer2` : `docker-compose-peer2.yml`
```yml
version: '2'

networks:
  basic:

services:
  peer2.org1.example.com:
    container_name: peer2.org1.example.com
    image: hyperledger/fabric-peer
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_PEER_ID=peer2.org1.example.com
      - CORE_LOGGING_PEER=info
      - CORE_CHAINCODE_LOGGING_LEVEL=info
      - CORE_PEER_LOCALMSPID=Org1MSP
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/msp/peer/
      - CORE_PEER_ADDRESS=peer2.org1.example.com:7051
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=${COMPOSE_PROJECT_NAME}_basic
      - CORE_LEDGER_STATE_STATEDATABASE=CouchDB
      - CORE_LEDGER_STATE_COUCHDBCONFIG_COUCHDBADDRESS=couchdb2:5984
      - CORE_LEDGER_STATE_COUCHDBCONFIG_USERNAME=
      - CORE_LEDGER_STATE_COUCHDBCONFIG_PASSWORD=
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: peer node start
    ports:
      - 9051:7051
      - 9053:7053
    volumes:
        - /var/run/:/host/var/run/
        - ./crypto-config/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp:/etc/hyperledger/msp/peer
        - ./crypto-config/peerOrganizations/org1.example.com/users:/etc/hyperledger/msp/users
        - ./config:/etc/hyperledger/configtx
    depends_on:
      - couchdb2
    networks:
      - basic

  couchdb2:
    container_name: couchdb2
    image: hyperledger/fabric-couchdb
    environment:
      - COUCHDB_USER=
      - COUCHDB_PASSWORD=
    ports:
      - 7984:5984
    networks:
      - basic
```

Run `peer2` container
```sh
#if windows, export this first
export COMPOSE_CONVERT_WINDOWS_PATHS=1

docker-compose -f ./docker-compose-peer2.yml up -d
```

Connect to `cli` container
```sh
docker exec -it cli bash
```

Redirect `cli` to `peer2`
```sh
export CORE_PEER_ADDRESS=peer2.org1.example.com:7051
```

Join `peer2` to `mychannel`
```sh
peer channel join -b mychannel.block
```

Check `peer2` is on `mychannel` now
```sh
peer channel list
```

---