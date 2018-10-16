## Chaincode installation & initiation

Install chaincode on `peer0` after fabric network is created
```sh
# Enter CLI
docker exec -it cli bash

# Point to peer0
export CORE_PEER_LOCALMSPID=Org1MSP
export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_ADDRESS=peer0.org1.example.com:7051

# Install chaincode on peer0
peer chaincode install \
  -n mycc \
  -v 1.0 \
  -p github.com/hyperledger/fabric/peer/chaincode/fabcar/ \
  -l golang

# Check chaincode is installed
peer chaincode list --installed

# Instantiate chaincode on peer0
peer chaincode instantiate \
  -o orderer.example.com:7050 \
  --tls \
  --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
  -C mychannel \
  -n mycc \
  -l golang \
  -v 1.0 \
  -c '{"Args":[""]}' \
  -P "OR ('Org1MSP.member')"

# Check chaincode is instantiated on channel
peer chaincode list --instantiated -C mychannel

# Test chaincode
# Init data
peer chaincode invoke \
  -o orderer.example.com:7050 \
  --tls true \
  --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
  -C mychannel \
  -n mycc \
  -c '{"function":"initLedger","Args":[""]}'

# Query data
peer chaincode invoke \
  -o orderer.example.com:7050 \
  --tls true \
  --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
  -C mychannel \
  -n mycc \
  -c '{"function":"queryAllCars","Args":[""]}'

```

If need to installed on `peer1`
```sh
# Point CLI to peer1
export CORE_PEER_LOCALMSPID=Org1MSP
export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt
export CORE_PEER_ADDRESS=peer1.org1.example.com:7051

# Install chaincode on peer1
peer chaincode install \
  -n mycc \
  -v 1.0 \
  -p github.com/hyperledger/fabric/peer/chaincode/fabcar/ \
  -l golang

# Query data from peer1
peer chaincode invoke \
  -o orderer.example.com:7050 \
  --tls true \
  --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
  -C mychannel \
  -n mycc \
  -c '{"function":"queryAllCars","Args":[""]}'
```