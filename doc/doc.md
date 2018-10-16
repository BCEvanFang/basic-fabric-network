## Start Network

Use scripts in network folder
```sh
cd ./network
```

Start network
```sh
./start.sh
```

Stop network
```
./stop.sh
```

Teardown network
```
./teardown.sh
```

Re-generate msp and artifacts for creating fabric network
```
./generate.sh
```

---

## Network topology

- Organization 1
  - Orderer ( *solo* )
  - Peer 0 with couchdb 0 ( *anchor peer* )
  - Peer 1 with couchdb 1
  - Channel ( *mychannel* )
  - CLI
  - ~~Fabric-ca ( *not in use* )~~