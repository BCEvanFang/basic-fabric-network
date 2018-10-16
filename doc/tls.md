## Enable Peer TLS

For peer, you need 4 env in docker-compose file
```sh
- CORE_PEER_TLS_ENABLED=true
- CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
- CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
- CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
```

And tls folder need to be added into volume
```
- ./crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls:/etc/hyperledger/fabric/tls
```

For example( *e.g. peer0* )
```yml
peer0.org1.example.com:
    container_name: peer0.org1.example.com
    image: hyperledger/fabric-peer
    environment:
      - ...
      # Enable TLS
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # |
      - ...
    working_dir: ...
    command: peer node start
    ports:
      - 7051:7051
      - 7053:7053
    volumes:
        - ...
        # TLS folder
        - ./crypto-config/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls:/etc/hyperledger/fabric/tls
        #
        - ...
    depends_on:
      - ...
    networks:
      - ...
```

CLI also need set `CORE_PEER_TLS_ENABLED=true` to enable tls for connnecting to peers
```yml
cli:
    container_name: cli
    image: hyperledger/fabric-tools
    tty: true
    environment:
      - ...
      # Enable TLS
      - CORE_PEER_TLS_ENABLED=true
      #
      - ...
    working_dir: ...
```