---
title: Ethermint私有连搭建-多节点网络.md
subtitle: Ethermint私有连搭建-多节点网络
tags: [ethermint,tendetmint,区块链,多节点]
date: 2018-4-10
---

## 前言
上篇已经已经基于Ethermint搭建了一个单节点，并在这个节点上进行了一些基本的操作：查看区块、链的状态、合约编译、部署以及简单的交互等。
本节主要讲解如何在这个基础上构建一个多节点的私有网络。  
  搭建一个多节点网络简单来说就是将新的节点与现有的节点建立连接，下面从建立新的节点并与已经存在的节点建立连接构建Ethermint网络


## 前置条件
- 版本信息
首先确保在不同节点上使用的版本尽量一致，本次部署使用的版本如下：
```
tendermint :0.14.0-88f5f21

ethermint:  0.5.3-deb7883
go-ethereum:  1.6.7-stable
```
<!-- more-->
- 初始化文件

为了初始化节点需要ethermint 和tendermint 基本配置文件，首先建立存放这些配置文件的目录：
```
makdir ethermint/ethermint
mkdir ethermint/tendermint
```
初始化文件主要是每一个ethermint和tendermint的geneis.json和一个用于tendermint的config.toml。可以参考已经启动的节点进行配置
- tendermint-genesis.json
查看已经启动节点的genesis.json配置，主要包括genesis_time\chain_id\validators\app_hash等信息，将这个文件拷贝到本机节点ethermint/tendermint目录，具体配置如下：
```
{
    "genesis_time": "0001-01-01T00:00:00Z", 
    "chain_id": "test-chain-rYaM42", 
    "validators": [
        {
            "pub_key": {
                "type": "ed25519", 
                "data": "721A2704D809CC9AB0414F8F7E3E7D0763110E2C66F99AA5843929549AB68B55"
            }, 
            "power": 10, 
            "name": ""
        }
    ], 
    "app_hash": ""
}
```
- tendermint-config.toml
查看已经启动节点的配置信息
```
# This is a TOML config file.
# For more information, see https://github.com/toml-lang/toml

proxy_app = "tcp://127.0.0.1:46658"
moniker = "srv-ubuntu-001"
fast_sync = true
db_backend = "leveldb"
log_level = "state:info,*:error"

[rpc]
laddr = "tcp://0.0.0.0:46657"

[p2p]
laddr = "tcp://0.0.0.0:46656"
seeds = "172.20.11.11:46656,172.20.11.12:46656"

```
seeds 是要要入网络中已经存在的节点，这里我们加入了之前已经启动的172.20.11.11:46656,172.20.11.12:46656两个内网节点  

moniker 节点的别名，可以自己任意设置  
将tendermint-config.toml拷贝到ethermint/tendermint目录

- ethermint genesis.json
我使用的ethermint是通过源码编译的，使用了默认的genesis.json。如果在使用了单独的配置，请将对应的genesis.json拷贝到ethermint/ethermint目录，下面是genesis.json的一个例子：
```
 "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "nonce": "0xdeadbeefdeadbeef",
    "timestamp": "0x00",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "difficulty": "0x40",
    "gasLimit": "0x8000000",
    "alloc": {
        "0x7eff122b94897ea5b0e2a9abf47b86337fafebdc": { "balance": "100000000000000" },
        "0xc6713982649D9284ff56c32655a9ECcCDA78422A": { "balance": "10000000000000000000000000000000000" }
    }
```


## 启动新节点并加入网络
- 初始化配置
```
ethermint --datadir ./ethermint init
INFO [04-09|17:47:21] Allocated cache and file handles         database=ethermint/ethermint/chaindata cache=16 handles=16
INFO [04-09|17:47:21] Writing custom genesis block 
INFO [04-09|17:47:21] successfully wrote genesis block and/or chain rule set hash=2b72f9…d468ac
```
- 启动tendermint和ethermint
执行如下命令启动tendermint和ethermint
```
tendermint --home ./ethermint/tendermint/ node &

ethermint --datadir ./ethermint --rpc --rpcaddr=0.0.0.0 --ws --wsaddr=0.0.0.0 --rpcapi eth,net,web3,personal,admin
```
可以看到如下log,
seeds = "172.20.11.11:46656,172.20.11.12"
``` 
ethermint --datadir ./ethermint --rpc --rpcaddr=0.0.0.0 --ws --wsaddr=0.0.0.0 --rpcapi eth,net,web3,personal,adminE[04-10|02:35:47.394] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-10|02:35:50.395] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-10|02:35:53.396] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query

INFO [04-10|10:35:53] Starting peer-to-peer node               instance=ethermint/v1.6.7-stable/linux-amd64/go1.9.3
INFO [04-10|10:35:53] Allocated cache and file handles         database=/home/bcadmin/test_net/ethermint/ethermint/chaindata cache=128 handles=1024
INFO [04-10|10:35:53] Initialised chain configuration          config="{ChainID: 15 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: <nil> EIP155: 0 EIP158: 0 Metropolis: <nil> Engine: unknown}"
WARN [04-10|10:35:53] Ethash used in fake mode 
INFO [04-10|10:35:53] Initialising Ethereum protocol           versions="[63 62]" network=1
INFO [04-10|10:35:53] Loaded most recent local header          number=0 hash=2b72f9…d468ac td=64
INFO [04-10|10:35:53] Loaded most recent local full block      number=0 hash=2b72f9…d468ac td=64
INFO [04-10|10:35:53] Loaded most recent local fast block      number=0 hash=2b72f9…d468ac td=64
INFO [04-10|10:35:53] Starting P2P networking 
INFO [04-10|10:35:53] RLPx listener up                         self="enode://5d08ceaa3bef92832f1017598e68ab05d732e60cf794471f8d5b28a6cf6dc34b815c6d7cfbe8f9100a89d41c17846b7fadf2ea71bb49473e824e82f6027f55ef@[::]:30303?discport=0"
INFO [04-10|10:35:53] HTTP endpoint opened: http://0.0.0.0:8545 
INFO [04-10|10:35:53] IPC endpoint opened: /home/bcadmin/test_net/ethermint/geth.ipc 
INFO [04-10|10:35:53] WebSocket endpoint opened: ws://0.0.0.0:8546 
INFO [04-10|10:35:53] Waiting for tendermint endpoint to start err="Post http://localhost:46657/status: dial tcp [::1]:46657: getsockopt: connection refused"


INFO [04-10|10:35:55] Starting ABCIServer                      module=abci-server impl=ABCIServer
INFO [04-10|10:35:55] Waiting for new connection...            module=abci-server
INFO [04-10|10:35:56] Accepted a new connection                module=abci-server
INFO [04-10|10:35:56] Waiting for new connection...            module=abci-server
INFO [04-10|10:35:56] Accepted a new connection                module=abci-server
INFO [04-10|10:35:56] Waiting for new connection...            module=abci-server
INFO [04-10|10:35:56] Accepted a new connection                module=abci-server
INFO [04-10|10:35:56] Waiting for new connection...            module=abci-server
E[04-10|02:35:59.412] Error in seed's address                      module=p2p err="Error in address 172.20.11.12: address 172.20.11.12: missing port in address"
I[04-10|02:36:01.274] Executed block                               module=state height=1 validTxs=0 invalidTxs=0
INFO [04-10|10:36:01] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=632.784µs mgasps=0.000 number=1 hash=54e78b…2439ec
I[04-10|02:36:01.279] Committed state                              module=state height=1 txs=0 hash=54E78B3DB3D7B301ECE58F04FA19BC850C65D3882531936D511924BFCC2439EC
I[04-10|02:36:01.293] Executed block                               module=state height=2 validTxs=0 invalidTxs=0
INFO [04-10|10:36:01] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=723.608µs mgasps=0.000 number=2 hash=c6762c…5ab53c
I[04-10|02:36:01.297] Committed state                              module=state height=2 txs=0 hash=C6762C2BA136C47D7E4B4B7ED22E8657B2ABE9835302DACEDA2D4D91495AB53C
I[04-10|02:36:02.174] Executed block                               module=state height=3 validTxs=0 invalidTxs=0
INFO [04-10|10:36:02] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=637.738µs mgasps=0.000 number=3 hash=02cb11…5abea8

```
成功连接了其他节点，已经已经开始同步块了，从上面的log当中其中有一段错误信息如下：
```
E[04-10|02:35:59.412] Error in seed's address                      module=p2p err="Error in address 172.20.11.12: address 172.20.11.12: missing port in address"
```
经查看是我在输入配置seeds时少配置了端口
```
seeds = "172.20.11.11:46656,172.20.11.12"
```

### 与新加入的节点进行交互
```
geth attach http://172.20.11.13:8545
Welcome to the Geth JavaScript console!

instance: ethermint/v1.6.7-stable/linux-amd64/go1.9.3
coinbase: 0x7eff122b94897ea5b0e2a9abf47b86337fafebdc
at block: 73429 (Tue, 10 Apr 2018 13:09:17 CST)
 datadir: /home/bcadmin/test_net/ethermint
 modules: admin:1.0 eth:1.0 net:1.0 personal:1.0 rpc:1.0 web3:1.0

> 
> eth.blockNumber
73467


```
## 一些疑问：
从私有链网络搭建过程中，可以看出还有很多问题没有明白，现简单整理一下，日后调查：
- tendermint与ethermint的详细参数设置以及含义
- tendermint负责网络和共识，监控网络状态的工具等
- ethermint集成了以太坊区块链存储合约等其他功能，geth工具也可以使用，它和tendermint如何集成的，修改了哪些东西，原理和结构层次还不太清晰
- 在搭建过程中发现ethermint 还是存在一些bug的，目前官方已经基本没有多少更新
- 其他待整理

## 参考资料
- 连接ethermint venus 测试网络： http://ethermint.readthedocs.io/en/develop/testnets/venus.html