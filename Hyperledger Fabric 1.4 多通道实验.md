# Hyperledger Fabric 1.4 多通道实验



## Hyperledger Fabric多通道网络实验环境搭建

Step 1：在Hyperledger官方提供的fabric-samples目录下克隆本教程提供的示例代码：

```shell
cd fabric-samples
git clone https://github.com/kctam/3org2ch_143.git
cd 3org2ch_143
```

Step 2：为参与Fabric通道的机构生成所需的密码学资料

```shell
../bin/cryptogen generate --config=./3org2ch_143-master/crypto-config.yaml
```

Step 3：生成Fabric通道素材

```shell
# 这里的pwd是当前的路径，/go/src/github.com/hyperledger/fabric-samples/3org2ch_143
# 而 configtx.yaml 和 crypto-config.yaml 在 3org2ch_143/3org2ch_143-master
# 所以要先 cd 3org2ch_143-master
# mkdir 还是在 3org2ch_143 
mkdir channel-artifacts && export FABRIC_CFG_PATH=$PWD

# 之后再 cd 3org2ch_143 
../bin/configtxgen -profile OrdererGenesis \
  -outputBlock ./channel-artifacts/genesis.block

export CHANNEL_ONE_NAME=channelall
export CHANNEL_ONE_PROFILE=ChannelAll
export CHANNEL_TWO_NAME=channel12
export CHANNEL_TWO_PROFILE=Channel12

../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} \
  -outputCreateChannelTx ./channel-artifacts/${CHANNEL_ONE_NAME}.tx \
  -channelID $CHANNEL_ONE_NAME

../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} \
  -outputCreateChannelTx ./channel-artifacts/${CHANNEL_TWO_NAME}.tx \
  -channelID $CHANNEL_TWO_NAME

../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} \
  -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors_${CHANNEL_ONE_NAME}.tx \
  -channelID $CHANNEL_ONE_NAME -asOrg Org1MSP

../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} \
  -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors_${CHANNEL_ONE_NAME}.tx \
  -channelID $CHANNEL_ONE_NAME -asOrg Org2MSP

../bin/configtxgen -profile ${CHANNEL_ONE_PROFILE} \
  -outputAnchorPeersUpdate ./channel-artifacts/Org3MSPanchors_${CHANNEL_ONE_NAME}.tx \
  -channelID $CHANNEL_ONE_NAME -asOrg Org3MSP

../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} \
  -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors_${CHANNEL_TWO_NAME}.tx \
  -channelID $CHANNEL_TWO_NAME -asOrg Org1MSP

../bin/configtxgen -profile ${CHANNEL_TWO_PROFILE} \
  -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors_${CHANNEL_TWO_NAME}.tx \
  -channelID $CHANNEL_TWO_NAME -asOrg Org2MSP
```

Step 4：启动所有的容器，最后应当看到有5个容器

```shell
# 还是要先进入到 3org2ch_143-master 路径下
docker-compose up -d
docker ps
```

Step 5：为了便于演示，开启3个终端，并设置排序节点的CA

Org1

```shell
docker exec -it cli bash 

export ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

```

Org2

```shell
docker exec -e "CORE_PEER_LOCALMSPID=Org2MSP" \
  -e "CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" \
  -e "CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp" \
  -e "CORE_PEER_ADDRESS=peer0.org2.example.com:7051" \
  -it cli bash

export ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
```

Org3

```shell
docker exec -e "CORE_PEER_LOCALMSPID=Org3MSP" \
  -e "CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org3.example.com/peers/peer0.org3.example.com/tls/ca.crt" \
  -e "CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp" \
  -e "CORE_PEER_ADDRESS=peer0.org3.example.com:7051" \
  -it cli bash

export ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

```



##### 清理运行环境

```shell
docker-compose down -v
docker rm $(docker ps -aq)
docker rmi $(docker images dev-* -q)
docker system prune --volumes
```

