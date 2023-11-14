# Hyperledger Fabric

版本: 2.2

## 准备阶段

| 需求           | 命令                       |
| -------------- | -------------------------- |
| 安装 Git       |                            |
| 安装 cURL      |                            |
| Docker         |                            |
| Docker Compose |                            |
| fabric         | bootstrip.sh -> bin+config |

## 更改主机

```sh
vim /etc/hosts
/etc/init.d/network restart
```

## 生成证书

```sh
#这里使用cryptogen工具来生成证书文件
cryptogen showtemplate > crypto-template.yaml  # 生成一份模板证书配置文件
-------------------------------------------------------------
 cryptogen generate --config=./crypto-template.yaml --output=crypto-config
```

## 生成创世块

```sh
#生成创世区块也需要一份配置文件configtx.yaml 我这里直接复制了test-network/configtx/configtx.yaml下面的
--------------------------------------------------------------
#弄好配置文件之后就可以使用configtxgen工具来生成创世区块了
configtxgen -configPath ./ -profile TwoOrgsOrdererGenesis -channelID system-channel -outputBlock ./artifacts/genesis.block

#profile	在配置文件中配置的Profile
#configPath	configtx.yaml的文件夹路径注意这里是文件夹的路径而不是具体的文件的路径
#channeID	system-channel 这是fabric的一个默认的通道
#outputBlock	要写入创世块的路径,包含文件名
```

## 启动/删除网络

```sh
docker-compose -f docker-compose.yaml up -d
docker-compose -f docker-compose.yaml down
docker volume prune
docker volume ls
```

## 生成通道和锚节点

```sh
--------------------------------------------------------------
#生成通道交易配置
configtxgen -configPath ./ -profile TwoOrgsChannel -channelID channel -outputCreateChannelTx ./artifacts/channel.tx
--------------------------------------------------------------
#生成两个组织的锚节点更新交易配置,每个组织一个，现在一般不用
configtxgen -configPath ./ -profile TwoOrgsChannel -channelID mychannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -asOrg Org1
## 这里的 -asOrg 的值是configtx中配置的组织的Name

```

## 创建通道加入组织并更新锚节点

```sh
#先配置组织身份环境变量
export CORE_PEER_LOCALMSPID=Org1MSP
export CORE_PEER_ADDRESS=localhost:7051
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_TLS_ENABLED=true
--------------------------------------------------------------
#使用我们之前生成的通道交易配置来创建通道证书的路径需要配置绝对路径使用相对路径会读不到
peer channel create -o orderer.orgbnk.com:7050 -c chswitch --ordererTLSHostnameOverride orderer.orgbnk.com -f ./channel-artifacts/chswitch.tx --outputBlock ./channel-artifacts/chswitch.block --tls --cafile ${PWD}/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem

peer channel create -o aa.aa.com:7070 -c channel --ordererTLSHostnameOverride aa.aa.com -f ./artifacts/channel.tx --outputBlock ./artifacts/channel.block --tls --cafile ${PWD}/crypto/ordererOrganizations/aa.com/orderers/aa.aa.com/msp/tlscacerts/tlsca.aa.com-cert.pem
--------------------------------------------------------------
#Org1加入通道并更新锚节点
peer channel join -b ./artifacts/chswitch.block


peer channel update -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

```

## 智能合约（链码）

### 1.链码打包

```sh
go mod init 项目名
go mod vendor
#进入cli容器
docker exec -it cli bash
# 打包链码
peer lifecycle chaincode package 本地路径/chswitch.tar.gz --path /opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/chswitch  --label chswitch_1
#退出容器，将打包的链码 mycc.tar.gz 拷贝到宿主机，然后拷贝到其他服务器
docker cp 文件路径 目标路径  （容器内部标明 容器名:路径）
scp 文件路径 目标用户@IP：绝对路径
-----------------
peer lifecycle chaincode package chswitch3.tar.gz --path /opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/chswitch  --label chswitch_3

# 使用 tar 命令打包lifecycle
CC_PACKAGE=src/brilliance/fast-deploy/
CC_TAR=bin/code.tar.gz
chaincode:
	tar --transform 's,^,${CC_PACKAGE},' -czf  ${CC_TAR} chaincode
	echo '{"path":"brilliance/fast-deploy/chaincode/signature","type":"golang","label":"pubsignature_1.0.0"}' > bin/metadata.json
	tar -zcf bin/signature.tar.gz -C bin metadata.json code.tar.gz
	rm -rf bin/code.tar.gz bin/metadata.json

#一般
tar --transform 's,^,src/brilliance/xapay/,' -czf xachaincode2.tar.gz chaincode
```

### 2.安装链码

```sh
#进入cli容器
docker cp sacc.tar.gz cli1:/opt/gopath/src/github.com/hyperledger/fabric/peer
#安装链码
peer lifecycle chaincode install chswitch3.tar.gz
#查询链码
peer lifecycle chaincode queryinstalled
```

### 3.批准链码

```sh
peer lifecycle chaincode approveformyorg --channelID chswitch --name swift --version 1 --init-required --package-id swift_1:deb230b06397ff1dd98ae035db574df6357c64dcf34202cfd66f122124d6a804 --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgent.com/msp/tlscacerts/tlsca.orgent.com-cert.pem -o orderer.orgent.com:7050

peer lifecycle chaincode approveformyorg --channelID chdb --name database --version 1 --init-required --package-id database_1:854bd2c6946593df26d3b6d7bfd1e7fd1b7cae1eb25452b3b1c9699624285359 --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem -o orderer.orgbnk.com:7050

peer lifecycle chaincode approveformyorg --channelID chswitch --name swift --version 1 --init-required --package-id swift_1:177b6845e1a6f031443b9ea9fe25200c093b3d761827cc213afb877913cbd9ee --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem -o orderer.orgbnk.com:7050

#查看链码的状态是否就绪
peer lifecycle chaincode checkcommitreadiness --channelID chswitch --name swift --version 1 --init-required --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --output json

peer lifecycle chaincode checkcommitreadiness --channelID chdb --name swift --version 1 --init-required --sequence 1 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --output json
```

### 4.提交链码

```sh
#提交链码定义，在 org1 或者 org2 上均可
peer lifecycle chaincode commit -o orderer.orgbnk.com:7050 --channelID chswitch --name swift --version 1 --sequence 1 --init-required --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --peerAddresses peer0.orgbnk.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgbnk.com/peers/peer0.orgbnk.com/tls/ca.crt --peerAddresses peer1.orgbnk.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgbnk.com/peers/peer0.orgbnk.com/tls/ca.crt


peer lifecycle chaincode commit -o orderer.orgbnk.com:7050 --channelID chdb --name database --version 1 --sequence 1 --init-required --tls true --cafile ${PWD}/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --peerAddresses peer0.orgbnk.com:7051 --tlsRootCertFiles ${PWD}/crypto-config/peerOrganizations/orgbnk.com/peers/peer0.orgbnk.com/tls/ca.crt --peerAddresses peer0.orgent.com:7051 --tlsRootCertFiles ${PWD}/crypto-config/peerOrganizations/orgent.com/peers/peer0.orgent.com/tls/ca.crt
--peerAddresses peer0.orggov.com:7051 --tlsRootCertFiles ${PWD}/crypto-config/peerOrganizations/orggov.com/peers/peer0.orggov.com/tls/ca.crt

peer lifecycle chaincode commit -o aa.aa.com:7070 --channelID channel --name chswitch3 --version 1.0 --sequence 1 --init-required --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/aa.com/orderers/aa.aa.com/msp/tlscacerts/tlsca.aa.com-cert.pem --peerAddresses peer0.orga.com:7071 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orga.com/peers/peer0.orga.com/tls/ca.crt

```

### 5.链码初始化与调用

```sh
peer chaincode invoke -o aa.aa.com:7070  --ordererTLSHostnameOverride aa.aa.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/aa.com/orderers/aa.aa.com/msp/tlscacerts/tlsca.aa.com-cert.pem -C channel -n chswitch3 --peerAddresses peer0.orga.com:7071 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orga.com/peers/peer0.orga.com/tls/ca.crt  -c   '{"Args":["PutMessage","{\"msg_id\":\"0001\",\"data\":\"111111\",\"sender\":\"OrgaMSP\",\"receivers\":[\"OrgaMSP\"]}"]}'  --waitForEvent
-----------------------------------
peer chaincode invoke -o aa.aa.com:7070  --ordererTLSHostnameOverride aa.aa.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/aa.com/orderers/aa.aa.com/msp/tlscacerts/tlsca.aa.com-cert.pem -C channel -n chswitch3 --peerAddresses peer0.orga.com:7071 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orga.com/peers/peer0.orga.com/tls/ca.crt  -c   '{"Args":["GetMessage","0003"]}'  --waitForEvent
---------------------------------
peer chaincode query -o aa.aa.com:7070  --ordererTLSHostnameOverride aa.aa.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/aa.com/orderers/aa.aa.com/msp/tlscacerts/tlsca.aa.com-cert.pem -C channel -n chswitch3 --peerAddresses peer0.orga.com:7071 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orga.com/peers/peer0.orga.com/tls/ca.crt  -c '{"Args":["GetInBox"]}'

 peer chaincode query -o orderer.exporgbnk.com:7050 --channelID chswitch --name swift  --ordererTLSHostnameOv
erride orderer.exporgbnk.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/com/orderers/orderer.expor
gbnk.com/msp/tlscacerts/tlsca.com-cert.pem --peerAddresses peer0.exporgbnk.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-
config/peerOrganizations/exporgbnk.com/peers/peer0.exporgbnk.com/tls/ca.crt     -c   '{"Args":["GetInBox"]}'
----------------------------------------------------------------
peer chaincode invoke -o aa.aa.com:7070 --isInit --ordererTLSHostnameOverride aa.aa.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/aa.com/orderers/aa.aa.com/msp/tlscacerts/tlsca.aa.com-cert.pem -C channel -n chswitch3 --peerAddresses peer0.orga.com:7071 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orga.com/peers/peer0.orga.com/tls/ca.crt  -c   '{"Args":["init"]}'  --waitForEvent


peer chaincode invoke -o orderer.orgbnk.com:7050 --channelID chswitch --name swift --isInit --ordererTLSHostnameOverride orderer.orgbnk.com --tls true --cafile ${PWD}/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --peerAddresses peer0.orgbnk.com:7051 --tlsRootCertFiles ${PWD}/crypto-config/peerOrganizations/orgbnk.com/peers/peer0.orgbnk.com/tls/ca.crt --peerAddresses peer0.orgent.com:7051 --tlsRootCertFiles ${PWD}/crypto-config/peerOrganizations/orgent.com/peers/peer0.orgent.com/tls/ca.crt --peerAddresses peer0.orggov.com:7051 --tlsRootCertFiles ${PWD}/crypto-config/peerOrganizations/orggov.com/peers/peer0.orggov.com/tls/ca.crt  -c   '{"Args":["init"]}'  --waitForEvent

peer chaincode invoke -o orderer.exporgbnk.com:7050 --channelID chdb --name database  --ordererTLSHostnameOverride orderer.orgbnk.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --peerAddresses peer0.exporgbnk.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgbnk.com/peers/peer0.orgbnk.com/tls/ca.crt --peerAddresses peer0.orgent.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgent.com/peers/peer0.orgent.com/tls/ca.crt    -c  '{"Args":["SetData","{\"data_id\":\"abc55\",\"data\":\"1552\"}"]}' --waitForEvent
'{"Args":["GetData","abc05"]}'
'{"Args":["SetData","{\"data_id\":\"abc55\",\"data\":\"1552\"}"]}'
'{"Args":["SetData","{\"data_id\":\"a464655\",\"data\":\"1554562\"}"]}'

peer chaincode invoke -o orderer.orgbnk.com:7050 --channelID chdb --name database  --ordererTLSHostnameOverride orderer.orgbnk.com --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/ordererOrganizations/orgbnk.com/orderers/orderer.orgbnk.com/msp/tlscacerts/tlsca.orgbnk.com-cert.pem --peerAddresses peer0.orgbnk.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgbnk.com/peers/peer0.orgbnk.com/tls/ca.crt --peerAddresses peer0.orgent.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgent.com/peers/peer0.orgent.com/tls/ca.crt    -c   '{"Args":["init"]}'  --waitForEvent



--peerAddresses peer0.orgent.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto-config/peerOrganizations/orgent.com/peers/peer0.orgent.com/tls/ca.crt  --waitForEvent
'{"Args":["PutMessage","{\"msg_id\":\"0002\",\"data\":\"1111110101\",\"sender\":\"OrgBnkPeer\",\"receivers\":[\"OrgEntPeer\",\"OrgBnkPeer\"]}"]}'
'{"Args":["GetMessage","0002"]}'
'{"Args":["GetInBox"]}'
```

## 常用

```shell
-----------------------------------
1、启动所有容器
docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
2、关闭所有容器
docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)
3、删除所有容器
docker rm $(docker ps -a | awk '{ print $1}' | tail -n +2)
4、删除所有镜像（慎用）
docker rmi $(docker images | awk '{print $3}' |tail -n +2)
---------------------------------
#进程删除
docker rm -f $(docker ps -a -q -f status=dead)
grep 394711f76d5417ffbd619a035d033b2276d92263f862b159415e078c66925c44 /proc/*/mountinfo
docker rm -f cli1
-----------------------------------------
docker rm peer0.lxh.testfabric.com
docker-compose create peer0.lxh.testfabric.com
1.netstat  -anp  |grep   端口号
2.netstat   -nultp（此处不用加端口号）
3.netstat  -anp  |grep  82查看82端口的使用情况
-----------------------
#常见错误解决方法
重启docker服务 systemctl restart docker
关闭防火墙

discover --configFile conf.yaml --peerTLSCA tls/ca.crt --userKey msp/keystore/ea4f6a38ac7057b6fa9502c2f5f39f182e320f71f667749100fe7dd94c23ce43_sk --userCert msp/signcerts/User1\@org1.example.com-cert.pem  --MSP Org1MSP saveConfig


-----------------
Error: got unexpected status: FORBIDDEN -- implicit policy evaluation failed - 0 sub-policies were satisfied, but this policy requires 1 of the 'Writers' sub-policies to be satisfied: permission denied
```

## 附录

### docker-compose

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: "2"

volumes:
  orderer.lxh.testfabric.com:
  peer0.lxh.testfabric.com:
  peer1.lxh.testfabric.com:

networks:
  test:
    name: sec-network_test

services:
  orderer.lxh.testfabric.com:
    container_name: orderer.lxh.testfabric.com
    image: hyperledger/fabric-orderer:2.2.3
    environment:
      - FABRIC_LOGGING_SPEC=DEBUG
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_LISTENPORT=7050
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSPlxh
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      - ORDERER_OPERATIONS_LISTENADDRESS=0.0.0.0:17050
      # enabled TLS
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
      - ORDERER_KAFKA_TOPIC_REPLICATIONFACTOR=1
      - ORDERER_KAFKA_VERBOSE=true
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
      - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
      - ./crypto-config/ordererOrganizations/lxh.testfabric.com/orderers/orderer.lxh.testfabric.com/msp:/var/hyperledger/orderer/msp
      - ./crypto-config/ordererOrganizations/lxh.testfabric.com/orderers/orderer.lxh.testfabric.com/tls/:/var/hyperledger/orderer/tls
      - orderer.lxh.testfabric.com:/var/hyperledger/production/orderer
    ports:
      - 7050:7050
      - 17050:17050
    networks:
      - test
    extra_hosts:
      - "orderer.chj.testfabric.com:192.168.10.34"
      - "orderer.lxh.testfabric.com:192.168.10.71"
      - "orderer.zzp.testfabric.com:192.168.10.24"
      - "peer0.zzp.testfabric.com:192.168.10.24"
      - "peer1.zzp.testfabric.com:192.168.10.24"
      - "peer0.chj.testfabric.com:192.168.10.34"
      - "peer1.chj.testfabric.com:192.168.10.34"
      - "peer0.lxh.testfabric.com:192.168.10.71"
      - "peer1.lxh.testfabric.com:192.168.10.71"
  peer0.lxh.testfabric.com:
    container_name: peer0.lxh.testfabric.com
    image: hyperledger/fabric-peer:2.2.3
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      # the following setting starts chaincode containers on the same
      # bridge network as the peers
      # https://docs.docker.com/compose/networking/
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=Orglxh_test
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variabes
      - CORE_PEER_ID=peer0.lxh.testfabric.com
      - CORE_PEER_ADDRESS=peer0.lxh.testfabric.com:7051
      - CORE_PEER_LISTENADDRESS=0.0.0.0:7051
      - CORE_PEER_CHAINCODEADDRESS=peer0.lxh.testfabric.com:7052
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:7052
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.lxh.testfabric.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.lxh.testfabric.com:7051
      - CORE_PEER_LOCALMSPID=OrgMSPlxh
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:17051
    volumes:
      - /var/run/docker.sock:/host/var/run/docker.sock
      - ./crypto-config/peerOrganizations/lxh.testfabric.com/peers/peer0.lxh.testfabric.com/msp:/etc/hyperledger/fabric/msp
      - ./crypto-config/peerOrganizations/lxh.testfabric.com/peers/peer0.lxh.testfabric.com/tls:/etc/hyperledger/fabric/tls
      - peer0.lxh.testfabric.com:/var/hyperledger/production
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    ports:
      - 7051:7051
      - 17051:17051
    networks:
      - test
    extra_hosts:
      - "orderer.chj.testfabric.com:192.168.10.34"
      - "orderer.lxh.testfabric.com:192.168.10.71"
      - "orderer.zzp.testfabric.com:192.168.10.24"

  peer1.lxh.testfabric.com:
    container_name: peer1.lxh.testfabric.com
    image: hyperledger/fabric-peer:2.2.3
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      # the following setting starts chaincode containers on the same
      # bridge network as the peers
      # https://docs.docker.com/compose/networking/
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=Orglxh_test
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variabes
      - CORE_PEER_ID=peer1.lxh.testfabric.com
      - CORE_PEER_ADDRESS=peer1.lxh.testfabric.com:9051
      - CORE_PEER_LISTENADDRESS=0.0.0.0:9051
      - CORE_PEER_CHAINCODEADDRESS=peer1.lxh.testfabric.com:9052
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:9052
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.lxh.testfabric.com:9051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.lxh.testfabric.com:9051
      - CORE_PEER_LOCALMSPID=OrgMSPlxh
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:19051
    volumes:
      - /var/run/docker.sock:/host/var/run/docker.sock
      - ./crypto-config/peerOrganizations/lxh.testfabric.com/peers/peer1.lxh.testfabric.com/msp:/etc/hyperledger/fabric/msp
      - ./crypto-config/peerOrganizations/lxh.testfabric.com/peers/peer1.lxh.testfabric.com/tls:/etc/hyperledger/fabric/tls
      - peer1.lxh.testfabric.com:/var/hyperledger/production
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    ports:
      - 9051:9051
      - 19051:19051
    networks:
      - test
    extra_hosts:
      - "orderer.chj.testfabric.com:192.168.10.34"
      - "orderer.lxh.testfabric.com:192.168.10.71"
      - "orderer.zzp.testfabric.com:192.168.10.24"

  cli1:
    container_name: cli1
    image: hyperledger/fabric-tools:2.2.3
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_ID=cil1
      - CORE_PEER_ADDRESS=peer0.lxh.testfabric.com:7051
      - CORE_PEER_LOCALMSPID=OrgMSPlxh
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/lxh.testfabric.com/peers/peer0.lxh.testfabric.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/lxh.testfabric.com/users/Admin@lxh.testfabric.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
      - /var/run/:/host/var/run/
      - ./chaincode/go/:/opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/go
      - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
      - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
      # - ../organizations:/opt/gopath/src/github.com/hyperledger/fabric/peer/organizations
      # - ../scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
    depends_on:
      - peer0.lxh.testfabric.com
    #   - peer0.org1.example.com
    #   - peer0.org2.example.com
    networks:
      - test
    extra_hosts:
      - "orderer.chj.testfabric.com:192.168.10.34"
      - "orderer.lxh.testfabric.com:192.168.10.71"
      - "orderer.zzp.testfabric.com:192.168.10.24"
      - "peer0.zzp.testfabric.com:192.168.10.24"
      - "peer1.zzp.testfabric.com:192.168.10.24"
      - "peer0.chj.testfabric.com:192.168.10.34"
      - "peer1.chj.testfabric.com:192.168.10.34"
      - "peer0.lxh.testfabric.com:192.168.10.71"
      - "peer1.lxh.testfabric.com:192.168.10.71"
  cli2:
    container_name: cli2
    image: hyperledger/fabric-tools:2.2.3
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_ID=cil2
      - CORE_PEER_ADDRESS=peer1.lxh.testfabric.com:9051
      - CORE_PEER_LOCALMSPID=OrgMSPlxh
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/lxh.testfabric.com/peers/peer1.lxh.testfabric.com/tls/ca.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/lxh.testfabric.com/users/Admin@lxh.testfabric.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
      - /var/run/:/host/var/run/
      - ./chaincode/go/:/opt/gopath/src/github.com/hyperledger/fabric-cluster/chaincode/go
      - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
      - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
      # - ../organizations:/opt/gopath/src/github.com/hyperledger/fabric/peer/organizations
      # - ../scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
    depends_on:
      #   - peer0.org1.example.com
      #   - peer0.org2.example.com
      - peer1.lxh.testfabric.com
    networks:
      - test
    extra_hosts:
      - "orderer.chj.testfabric.com:192.168.10.34"
      - "orderer.lxh.testfabric.com:192.168.10.71"
      - "orderer.zzp.testfabric.com:192.168.10.24"
      - "peer0.zzp.testfabric.com:192.168.10.24"
      - "peer1.zzp.testfabric.com:192.168.10.24"
      - "peer0.chj.testfabric.com:192.168.10.34"
      - "peer1.chj.testfabric.com:192.168.10.34"
      - "peer0.lxh.testfabric.com:192.168.10.71"
      - "peer1.lxh.testfabric.com:192.168.10.71"
```

### configtx

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
  # SampleOrg defines an MSP using the sampleconfig.  It should never be used
  # in production but may be used as a template for other definitions
  - &OrdererOrgzzp
    # DefaultOrg defines the organization which is used in the sampleconfig
    # of the fabric.git development environment
    Name: OrdererOrgzzp

    # ID to load the MSP definition as
    ID: OrdererMSPzzp

    # MSPDir is the filesystem path which contains the MSP configuration
    MSPDir: crypto-config/ordererOrganizations/zzp.testfabric.com/msp

    # Policies defines the set of policies at this level of the config tree
    # For organization policies, their canonical path is usually
    #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
    Policies:
      Readers:
        Type: Signature
        Rule: "OR('OrdererMSPzzp.member')"
      Writers:
        Type: Signature
        Rule: "OR('OrdererMSPzzp.member')"
      Admins:
        Type: Signature
        Rule: "OR('OrdererMSPzzp.admin')"

    OrdererEndpoints:
      - orderer.zzp.testfabric.com:7050
  - &OrdererOrglxh
    # DefaultOrg defines the organization which is used in the sampleconfig
    # of the fabric.git development environment
    Name: OrdererOrglxh

    # ID to load the MSP definition as
    ID: OrdererMSPlxh

    # MSPDir is the filesystem path which contains the MSP configuration
    MSPDir: crypto-config/ordererOrganizations/lxh.testfabric.com/msp

    # Policies defines the set of policies at this level of the config tree
    # For organization policies, their canonical path is usually
    #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
    Policies:
      Readers:
        Type: Signature
        Rule: "OR('OrdererMSPlxh.member')"
      Writers:
        Type: Signature
        Rule: "OR('OrdererMSPlxh.member')"
      Admins:
        Type: Signature
        Rule: "OR('OrdererMSPlxh.admin')"

    OrdererEndpoints:
      - orderer.lxh.testfabric.com:7050
  - &OrdererOrgchj
    # DefaultOrg defines the organization which is used in the sampleconfig
    # of the fabric.git development environment
    Name: OrdererOrgchj

    # ID to load the MSP definition as
    ID: OrdererMSPchj

    # MSPDir is the filesystem path which contains the MSP configuration
    MSPDir: crypto-config/ordererOrganizations/chj.testfabric.com/msp

    # Policies defines the set of policies at this level of the config tree
    # For organization policies, their canonical path is usually
    #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
    Policies:
      Readers:
        Type: Signature
        Rule: "OR('OrdererMSPchj.member')"
      Writers:
        Type: Signature
        Rule: "OR('OrdererMSPchj.member')"
      Admins:
        Type: Signature
        Rule: "OR('OrdererMSPchj.admin')"

    OrdererEndpoints:
      - orderer.chj.testfabric.com:7050

  - &OrgZZP
    # DefaultOrg defines the organization which is used in the sampleconfig
    # of the fabric.git development environment
    Name: OrgMSPzzp

    # ID to load the MSP definition as
    ID: OrgMSPzzp

    MSPDir: crypto-config/peerOrganizations/zzp.testfabric.com/msp

    # Policies defines the set of policies at this level of the config tree
    # For organization policies, their canonical path is usually
    #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
    Policies:
      Readers:
        Type: Signature
        Rule: "OR('OrgMSPzzp.admin', 'OrgMSPzzp.peer', 'OrgMSPzzp.client')"
      Writers:
        Type: Signature
        Rule: "OR('OrgMSPzzp.admin', 'OrgMSPzzp.client')"
      Admins:
        Type: Signature
        Rule: "OR('OrgMSPzzp.admin')"
      Endorsement:
        Type: Signature
        Rule: "OR('OrgMSPzzp.peer')"
    AnchorPeers:
      - Host: peer0.zzp.testfabric.com
        Port: 7051
      - Host: peer1.zzp.testfabric.com
        Port: 7051

  - &OrgLXH
    # DefaultOrg defines the organization which is used in the sampleconfig
    # of the fabric.git development environment
    Name: OrgMSPlxh

    # ID to load the MSP definition as
    ID: OrgMSPlxh

    MSPDir: crypto-config/peerOrganizations/lxh.testfabric.com/msp

    # Policies defines the set of policies at this level of the config tree
    # For organization policies, their canonical path is usually
    #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
    Policies:
      Readers:
        Type: Signature
        Rule: "OR('OrgMSPlxh.admin', 'OrgMSPlxh.peer', 'OrgMSPlxh.client')"
      Writers:
        Type: Signature
        Rule: "OR('OrgMSPlxh.admin', 'OrgMSPlxh.client')"
      Admins:
        Type: Signature
        Rule: "OR('OrgMSPlxh.admin')"
      Endorsement:
        Type: Signature
        Rule: "OR('OrgMSPlxh.peer')"
    AnchorPeers:
      - Host: peer0.lxh.testfabric.com
        Port: 7051
      - Host: peer1.lxh.testfabric.com
        Port: 7051
  - &OrgCHJ
    # DefaultOrg defines the organization which is used in the sampleconfig
    # of the fabric.git development environment
    Name: OrgMSPchj

    # ID to load the MSP definition as
    ID: OrgMSPchj

    MSPDir: crypto-config/peerOrganizations/chj.testfabric.com/msp

    # Policies defines the set of policies at this level of the config tree
    # For organization policies, their canonical path is usually
    #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
    Policies:
      Readers:
        Type: Signature
        Rule: "OR('OrgMSPchj.admin', 'OrgMSPchj.peer', 'OrgMSPchj.client')"
      Writers:
        Type: Signature
        Rule: "OR('OrgMSPchj.admin', 'OrgMSPchj.client')"
      Admins:
        Type: Signature
        Rule: "OR('OrgMSPchj.admin')"
      Endorsement:
        Type: Signature
        Rule: "OR('OrgMSPchj.peer')"
    AnchorPeers:
      - Host: peer0.chj.testfabric.com
        Port: 7051
      - Host: peer1.chj.testfabric.com
        Port: 7051

Capabilities:
  # Channel capabilities apply to both the orderers and the peers and must be
  # supported by both.
  # Set the value of the capability to true to require it.
  Channel: &ChannelCapabilities
    V2_0: true

  Orderer: &OrdererCapabilities
    V2_0: true

  Application: &ApplicationCapabilities
    V2_0: true

Application: &ApplicationDefaults
  # Organizations is the list of orgs which are defined as participants on
  # the application side of the network
  Organizations:

  # Policies defines the set of policies at this level of the config tree
  # For Application policies, their canonical path is
  #   /Channel/Application/<PolicyName>
  Policies:
    Readers:
      Type: ImplicitMeta
      Rule: "ANY Readers"
    Writers:
      Type: ImplicitMeta
      Rule: "ANY Writers"
    Admins:
      Type: ImplicitMeta
      Rule: "MAJORITY Admins"
    LifecycleEndorsement:
      Type: ImplicitMeta
      Rule: "MAJORITY Endorsement"
    Endorsement:
      Type: ImplicitMeta
      Rule: "MAJORITY Endorsement"

  Capabilities:
    <<: *ApplicationCapabilities
################################################################################
#
#   SECTION: Orderer
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for orderer related parameters
#
################################################################################
Orderer: &OrdererDefaults # Orderer Type: The orderer implementation to start
  OrdererType: etcdraft

  Addresses:
    - orderer.zzp.testfabric.com:7050
    - orderer.lxh.testfabric.com:7050
    - orderer.chj.testfabric.com:7050
  EtcdRaft:
    Consenters:
      - Host: orderer.zzp.testfabric.com
        Port: 7050
        #crypto-config/ordererOrganizations/zzp.testfabric.com/msp
        ClientTLSCert: crypto-config/ordererOrganizations/zzp.testfabric.com/orderers/orderer.zzp.testfabric.com/tls/server.crt
        ServerTLSCert: crypto-config/ordererOrganizations/zzp.testfabric.com/orderers/orderer.zzp.testfabric.com/tls/server.crt
      - Host: orderer.lxh.testfabric.com
        Port: 7050
        #crypto-config/ordererOrganizations/zzp.testfabric.com/msp
        ClientTLSCert: crypto-config/ordererOrganizations/lxh.testfabric.com/orderers/orderer.lxh.testfabric.com/tls/server.crt
        ServerTLSCert: crypto-config/ordererOrganizations/lxh.testfabric.com/orderers/orderer.lxh.testfabric.com/tls/server.crt
      - Host: orderer.chj.testfabric.com
        Port: 7050
        #crypto-config/ordererOrganizations/zzp.testfabric.com/msp
        ClientTLSCert: crypto-config/ordererOrganizations/chj.testfabric.com/orderers/orderer.chj.testfabric.com/tls/server.crt
        ServerTLSCert: crypto-config/ordererOrganizations/chj.testfabric.com/orderers/orderer.chj.testfabric.com/tls/server.crt
  # Batch Timeout: The amount of time to wait before creating a batch
  BatchTimeout: 2s

  # Batch Size: Controls the number of messages batched into a block
  BatchSize:
    # Max Message Count: The maximum number of messages to permit in a batch
    MaxMessageCount: 10

    # Absolute Max Bytes: The absolute maximum number of bytes allowed for
    # the serialized messages in a batch.
    AbsoluteMaxBytes: 99 MB

    # Preferred Max Bytes: The preferred maximum number of bytes allowed for
    # the serialized messages in a batch. A message larger than the preferred
    # max bytes will result in a batch larger than preferred max bytes.
    PreferredMaxBytes: 512 KB

  # Organizations is the list of orgs which are defined as participants on
  # the orderer side of the network
  Organizations:

  # Policies defines the set of policies at this level of the config tree
  # For Orderer policies, their canonical path is
  #   /Channel/Orderer/<PolicyName>
  Policies:
    Readers:
      Type: ImplicitMeta
      Rule: "ANY Readers"
    Writers:
      Type: ImplicitMeta
      Rule: "ANY Writers"
    Admins:
      Type: ImplicitMeta
      Rule: "MAJORITY Admins"
    # BlockValidation specifies what signatures must be included in the block
    # from the orderer for the peer to validate it.
    BlockValidation:
      Type: ImplicitMeta
      Rule: "ANY Writers"

################################################################################
#
#   CHANNEL
#
#   This section defines the values to encode into a config transaction or
#   genesis block for channel related parameters.
#
################################################################################
Channel: &ChannelDefaults
  # Policies defines the set of policies at this level of the config tree
  # For Channel policies, their canonical path is
  #   /Channel/<PolicyName>
  Policies:
    # Who may invoke the 'Deliver' API
    Readers:
      Type: ImplicitMeta
      Rule: "ANY Readers"
    # Who may invoke the 'Broadcast' API
    Writers:
      Type: ImplicitMeta
      Rule: "ANY Writers"
    # By default, who may modify elements at this config level
    Admins:
      Type: ImplicitMeta
      Rule: "MAJORITY Admins"

  # Capabilities describes the channel level capabilities, see the
  # dedicated Capabilities section elsewhere in this file for a full
  # description
  Capabilities:
    <<: *ChannelCapabilities

################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################
Profiles:
  TwoOrgsOrdererGenesis:
    <<: *ChannelDefaults
    Orderer:
      <<: *OrdererDefaults
      Organizations:
        - *OrdererOrgzzp
        - *OrdererOrglxh
        - *OrdererOrgchj
      Capabilities:
        <<: *OrdererCapabilities
    Consortiums:
      SampleConsortium:
        Organizations:
          - *OrgZZP
          - *OrgLXH
          - *OrgCHJ
  TwoOrgsChannel:
    Consortium: SampleConsortium
    <<: *ChannelDefaults
    Application:
      <<: *ApplicationDefaults
      Organizations:
        - *OrgZZP
        - *OrgLXH
        - *OrgCHJ
      Capabilities:
        <<: *ApplicationCapabilities
```

### crypto-template

```yaml
#crypto-template.yaml
OrdererOrgs: # orderer组织的配置
  - Name: Orderer # 组织名称
    Domain: example.com # 组织的域名
    EnableNodeOUs: true # 设置了EnableNodeOUs，会在map下生成config.yaml文件
    Specs:
      - Hostname: orderer # 生成的证书文件会以{{Hostname}}.{{Domain}}呈现　当前的文件名就是
        SANS:
          - localhost # orderer.example.com
#注意:这里的SANS一定要填节点真实的地址,不然会导致后面加入通道加入不进去
PeerOrgs:
  - Name: Org1
    Domain: org1.example.com
    EnableNodeOUs: true
    # template根据count来生成指定份数的证书文件
    # 我的理解是给当前这个组织中的节点生成证书文件　
    # 例如一个组织中有3个节点那么这个count就是3
    Template:
      Count: 1
      SANS:
        - localhost
      # Start: 5   指定生成的节点从几开始，不指定就从0开始　peer0.org1.example.com
    Users: # 组织中除了Admin之外还需要生成多少个用户，数量由count决定
      Count: 1

  - Name: Org2
    Domain: org2.example.com
    EnableNodeOUs: true
    Template:
      Count: 1
      SANS:
        - localhost
    Users:
      Count: 1
```

## 智能合约

### 1. go

```go
import("github.com/hyperledger/fabric-chaincode-go/shim"
//别名pb
pb "github.com/hyperledger/fabric-protos-go/peer")
```

#### 1.1 概述

​ 链码的编写需要自定义 struct，实现 shim 包 Chaincode 接口的两个方法:

```golang
type Chaincode interface {
  Init(stub ChaincodeStubInterface) pb.Response
  Invoke(stub ChaincodeStubInterface) pb.Response
}
```

​ 链码的生命周期包括链码安装、实例化、升级等。其中链码的实例化和升级都会调用 Init()方法，链码的 invoke、query 调用方式都只会调用 Invoke()方法。
在 shim 包中，fabric 给我们提供了 ChaincodeStubInterface 接口，在该接口中，我们可以使用接口中的方法实现具体的链码业务。

      特别说明一句，如果链码方法最后返回的pb.Response是shim.Error()，那么所有的操作，包括数据插入、数据删除、创建复合键、发送事件、调用其他链码等都是无效的，即一次链码的调用具有事务性，如果返回shim.Success()则所有操作都成功，否则所有操作都失败。

4、图解

- ![asdasd](\photo\fab001.png)

# fabric 网络搭建问题汇总

1. `/etc/hosts`配置网络
2. 网络不通，关闭防火墙
3. `docker-compose.yaml`文件，非锚节点`peer`节点的`CORE_PEER_GOSSIP_BOOTSTRAP=锚节点地址：端口号`
4. 网络启动报错，重启`docker`服务
5. 链码使用复合键的记录，也要通过查询复合键索引的方式查询记录
6. 链码批准、提交时加入背书策略`--signature-policy "OR ('OrgBnkPeer.peer','OrgGovPeer.peer','OrgEntPeer.peer')"`后链码初始化、调用可以仅使用一组织证书

# [Fabric-SDK-go]

## 1、fabric-sdk-go 简介

fabric-sdk-go 是 Fabric 官方的 Go 语言 SDK，它的目录结构如下：

![img](http://blog.hubwiz.com/2020/03/18/fabric-sdk-go-tutorial/fabric-sdk-go-directory.png)

有 2 个目录需要注意一下，internal 和 third_party，它们两个包含了 fabric-sdk-go 依赖的一些代码， 来自于 fabric、fabric-ca，当使用到 fabric 的一些类型时，应当使用以下的方式，而不是直接导入 fabric 或者 fabric-ca：

```
import "github.com/hyperledger/fabric-sdk-go/third_party/github.com/hyperledger/fabric/xxx"
```

pkg 目录是 fabric-sdk-go 的主要实现，doc 文档介绍了不同目录所提供的功能，以及给出 了接口调用样例：

- pkg/fabsdk：主 package，主要用来生成 fabsdk 以及 fabric-sdk-go 中其他 pkg 使用的 option context。
- pkg/client/channel：主要用来调用、查询 Fabric 链码，或者注册链码事件。
- pkg/client/resmgmt：主要用来 Hyperledger fabric 网络的管理，比如创建通道、加入通道，安装、实例化和升级链码。
- pkg/client/event:配合 channel 模块来进行 Fabric 链码事件的注册和过滤。
- pkg/client/ledger：主要用来实现 Fabric 账本的查询，查询区块、交易、配置等。
- pkg/client/msp：主要用来管理 fabric 网络中的成员关系。

## 3、使用 fabric-sdk-go 的一般步骤

- 为 client 编写配置文件 config.yaml
- 为 client 创建 fabric sdk 实例 fabsdk
- 为 client 创建 resource manage client，简称 RC，RC 用来进行管理操作的 client， 比如通道的创建，链码的安装、实例化和升级等
- 为 client 创建 channel client，简称 CC，CC 用来链码的调用、查询以及链码事件 的注册和取消注册

## 4、fabric-sdk-go 配置文件 config.yaml 概述

client 使用 sdk 与 fabric 网络交互，需要告诉 sdk 两类信息：

- 我是谁：即当前 client 的信息，包含所属组织、密钥和证书文件的路径等， 这是每个 client 专用的信息。
- 对方是谁：即 fabric 网络结构的信息，channel、org、orderer 和 peer 等 的怎么组合起当前 fabric 网络的，这些结构信息应当与 configytx.yaml 中是一致的。这是通用配置，每个客户端都可以拿来使用。另外，这部分信息并不需要是完整 fabric 网络信息，如果当前 client 只和部分节点交互，那配置文件中只需要包含所使用到的网络信息。

![img](http://blog.hubwiz.com/2020/03/18/fabric-sdk-go-tutorial/fabric-sdk-go-config.png)

## 5、使用 go mod 管理 fabric-sdk-go 项目的依赖

fabric-sdk-go 目前本身使用 go modules 管理依赖，从 go.mod 可知，依赖的一些包指定了具体的版本， 如果你的项目依赖的版本和 fabric-sdk-go 依赖的版本不同，会产生编译问题。

因此建议项目也使用 go moudles 管理依赖，然后相同的软件包可以使用相同的版本，可以这样操作：

- go mod init 初始化好项目的 go.mod 文件。
- 编写代码，完成后运行 go mod run，会自动下载依赖的项目，但版本可能与 fabric-sdk-go 中的依赖版本不同，编译存在问题。
- 把 go.mod 中的内容复制到项目的 go.mod 中，然后保存，go mod 会自动合并相同的依赖， 运行 go mod tidy，会自动添加新的依赖或删除不需要的依赖。

## 6、创建 fabric-sdk-go 的入口实例

通过 config.FromFile 解析配置文件，然后通过 fabsdk.New 创建 fabric-sdk-go 的入口实例。

```go
import "github.com/hyperledger/fabric-sdk-go/pkg/core/config"
import "github.com/hyperledger/fabric-sdk-go/pkg/fabsdk"
sdk, err := fabsdk.New(config.FromFile(c.ConfigPath))
if err != nil {  log.Panicf("failed to create fabric sdk: %s", err)}
```

## 7、创建 fabric-sdk-go 的资源管理客户端

管理员账号才能进行 Hyperledger fabric 网络的管理操作，所以创建资源管理客户端一定要 使用管理员账号。

通过 fabsdk.WithOrg(“Org1”)和 fabsdk.WithUser(“Admin”)指定 Org1 的 Admin 账户，使用 sdk.Context 创建 clientProvider，然后通过 resmgmt.New 创建 fabric-sdk-go 资源管理客户端。

```go
import 	"github.com/hyperledger/fabric-sdk-go/pkg/client/resmgmt"
rcp := sdk.Context(fabsdk.WithUser("Admin"), fabsdk.WithOrg("Org1"))
rc, err := resmgmt.New(rcp)
if err != nil {
    log.Panicf("failed to create resource client: %s", err)
}
```

## 8、创建 fabric-sdk-go 的通道客户端

使用用户账号创建 fabric-sdk-go 的通道客户端，以便进行 fabric 链码的调用和查询。 使用 sdk.ChannelContext 创建 channelProvider，需要指定 channelID 和用户 User1， 然后通过 channel.New 创建通道客户端，这个通道客户端就是调用 channelID 对应 channel 上链码的 channel client。

```go
import 	"github.com/hyperledger/fabric-sdk-go/pkg/client/channel"
ccp := sdk.ChannelContext(ChannelID, fabsdk.WithUser("User1"))
cc, err := channel.New(ccp)
if err != nil {
log.Panicf("failed to create channel client: %s", err)
}
```

## 9、使用 fabric-sdk-go 的资源管理客户端安装链码

安装 Fabric 链码使用资源管理客户端的 InstallCC 接口，需要指定 resmgmt.InstallCCRequest 以及 在哪些 peers 上面安装。resmgmt.InstallCCRequest 指明了链码 ID、链码路径、链码版本 以及打包后的链码。

打包链码需要使用到链码路径 CCPath 和 GoPath，GoPath 即本机的$GOPATH，CCPath 是相对 于 GoPath 的相对路径，如果路径设置不对，会造成 sdk 找不到链码。

```go
// pack the chaincode
ccPkg, err := gopackager.NewCCPackage("github.com/hyperledger/fabric-samples/chaincode/chaincode_example02/go/", "/Users/shitaibin/go")
if err != nil {
    return errors.WithMessage(err, "pack chaincode error")
}
// new request of installing chaincode
req := resmgmt.InstallCCRequest{
    Name:    c.CCID,
    Path:    c.CCPath,
    Version: v,
    Package: ccPkg,
}
reqPeers := resmgmt.WithTargetEndpoints("peer0.org1.example.com")
resps, err := rc.InstallCC(req, reqPeers)
if err != nil {
    return errors.WithMessage(err, "installCC error")
}
```

## 10、使用 fabric-sdk-go 的资源管理客户端实例化链码

实例化链码需要使用 fabric-sdk-go 的资源管理客户端的 InstantiateCC 接口，需要通过 ChannelID、 resmgmt.InstantiateCCRequest 和 peers，指明在哪个 channel 上实例化链码， 请求包含了链码的 ID、路径、版本，以及初始化参数和背书策略， 背书策略可以通过 cauthdsl.FromString 生成。

```go
// endorser policy
org1OrOrg2 := "OR('Org1MSP.member','Org2MSP.member')"
ccPolicy, err := cauthdsl.FromString(org1OrOrg2)
if err != nil {  return errors.WithMessage(err, "gen policy from string error")}
// new request
args := packArgs([]string{"init", "a", "100", "b", "200"})
req := resmgmt.InstantiateCCRequest{
    Name:    c.CCID,
    Path:    c.CCPath,
    Version: v,
    Args:    args,
    Policy:  ccPolicy,
}// send request and handle response
reqPeers := resmgmt.WithTargetEndpoints("peer0.org1.example.com")
resp, err := rc.InstantiateCC(ChannelID, req, reqPeers)
if err != nil {
    return errors.WithMessage(err, "instantiate chaincode error")
}
```

## 11、使用 fabric-sdk-go 的资源管理客户端升级链码

再 fabric-sdk-go 中，升级链码和实例化链码是非常相似的，不同点只在请求是 resmgmt.UpgradeCCRequest， 调用的接口是 rc.UpgradeCC：

```go
// endorser policy
org1AndOrg2 := "AND('Org1MSP.member','Org2MSP.member')"
ccPolicy, err := c.genPolicy(org1AndOrg2)
if err != nil {
    return errors.WithMessage(err, "gen policy from string error")
}
// new request
args := packArgs([]string{"init", "a", "100", "b", "200"})
req := resmgmt.UpgradeCCRequest{
    Name:    c.CCID,
    Path:    c.CCPath,
    Version: v,
    Args:    args,
    Policy:  ccPolicy,}// send request and handle response
reqPeers := resmgmt.WithTargetEndpoints("peer0.org1.example.com")
resp, err := rc.UpgradeCC(ChannelID, req, reqPeers)
if err != nil {
    return errors.WithMessage(err, "instantiate chaincode error")
}
```

## 12、使用 fabric-sdk-go 的通道客户端调用链码

在 fabric-sdk-go 中，使用通道客户端的 Execute 接口调用链码，使用入参 channel.Request 和 peers 指明要让哪些 peer 上执行链码，进行背书。channel.Request 指明了要调用的链码， 以及链码内要 Invoke 的函数 args，args 是序列化的结果，序列化是自定义的，只要链码 能够按相同的规则进行反序列化即可。

```go
// new channel request for invoke
args := packArgs([]string{"a", "b", "10"})
req := channel.Request{
    ChaincodeID: c.CCID,
    Fcn:         "invoke",
    Args:        args,
}
// send request and handle response
// peers is needed
reqPeers := channel.WithTargetEndpoints("peer0.org1.example.com")
resp, err := cc.Execute(req, reqPeers)
if err != nil {
    return errors.WithMessage(err, "invoke chaincode error")
}
log.Printf("invoke chaincode tx: %s", resp.TransactionID)
```

## 13、使用 fabric-sdk-go 的通道客户端查询链码

在 fabric-sdk-go 中，查询和调用链码是非常相似的，使用相同的 channel.Request， 指明了 Invoke 链码中的 query 函数，然后调用 cc.Query 进行查询操作，这样节点 不会对请求进行背书：

```go
// new channel request for query
req := channel.Request{
    ChaincodeID: c.CCID,
    Fcn:         "query",
    Args:        packArgs([]string{keys}),
}
// send request and handle response
reqPeers := channel.WithTargetEndpoints(peer)
resp, err := cc.Query(req, reqPeers)
if err != nil {  return errors.WithMessage(err, "query chaincode error")}
log.Printf("query chaincode tx: %s", resp.TransactionID)
log.Printf("result: %v", string(resp.Payload))
```

# 区块链浏览器

![在这里插入图片描述](\photo\区块链浏览器1.jpg)

![](D:\study\Learning-notes\photo\区块链浏览器2.jpg)
