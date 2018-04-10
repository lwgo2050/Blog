---
title: Ethermint私有链搭建-单机节点启动
subtitle: ethermint,tendetmint,private_net
tags: [ethermint,tendetmint,区块链]
date: 2018-4-9
---


## 前言
本文主要利用ethermint去启动一个节点，并在本节点是去执行交易和挖矿以及部署一个简单的合约，从实际的部署和使用中去了解ethermint的使用

## Ethermint安装
运行Ethermint需要安装ethermint和tendermint,主要有以下两种基本方式安装
- 二进制文件下载安装
- 源文件安装

> 这里不做详细阐述，会在其他篇幅中提供，后面会附上其他文章的联建

## Ethermint单机节点启动
### 初始化创始文件
在运行Ethermint之前需要初始化tendermint和ethermint的创世配置等信息，提供了创世账户及其默认金额，这个可以单独配置。如果是二进制文件下载安装，需要下载创世账户和创世文件下载地址https://github.com/tendermint/ethermint/tree/develop/setup。 如果您从源代码安装，则可以执行如下命令获得。
```
tendermint init --home ./ethermint/tendermint
ethermint --datadir ./ethermint init
```
执行完毕之后可以看到在ethermint/目录下生成keystore,在ethermint/tendermint下也会生成对应的config和genesis文件，具体这些创世文件的含义以及如何配置将会花单独的篇幅进行说明。
<!-- more-->
### 启动Ethermint
要执行ethermint，我们需要启动两个进程。 第一个是用于处理P2P通信以及共识流程的tendermint，而第二个实际上是ethermint，它提供了以太坊功能。

- 首先启动tendermint
- 
```
tendermint --home ./ethermint/tendermint node

E[04-09|02:46:48.995] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-09|02:46:51.995] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-09|02:46:54.996] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-09|02:46:57.996] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-09|02:47:00.997] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-09|02:47:03.997] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
E[04-09|02:47:06.998] abci.socketClient failed to connect to tcp://127.0.0.1:46658.  Retrying... module=abci-client connection=query
I[04-09|02:47:13.045] Executed block                               module=state height=1 validTxs=0 invalidTxs=0
I[04-09|02:47:13.047] Committed state                              module=state height=1 txs=0 hash=DCC7B97B1EA4449F16F92D98B4C93B77642D159A33EAA65DC96C895908E2FF0C
I[04-09|02:47:14.063] Executed block                               module=state height=2 validTxs=0 invalidTxs=0
I[04-09|02:47:14.064] Committed state                              module=state height=2 txs=0 hash=32EC1A597B49BD5130C7A49066129231EEB0E52F620EA749B1F0E15037F21365
I[04-09|02:47:15.083] Executed block                               module=state height=3 validTxs=0 invalidTxs=0
I[04-09|02:47:15.085] Committed state                              module=state height=3 txs=0 hash=5909F1BE30EB4B00ACF345E17AAA00C89C7E2CDE2329C724F46B3ADC63C05090
I[04-09|02:47:16.102] Executed block                               module=state height=4 validTxs=0 invalidTxs=0
I[04-09|02:47:16.104] Committed state                              module=state height=4 txs=0 hash=F44A8DEB58C768430AFACB8D35351E3E62D239F39DB797F8A280030C73F11575
I[04-09|02:47:17.121] Executed block                               module=state height=5 validTxs=0 invalidTxs=0
I[04-09|02:47:17.123] Committed state                              module=state height=5 txs=0 hash=F7775DC5DEB7E41905CB4314710535C43208E3303CBAF3A5678E11FC661B4C71
I[04-09|02:47:18.139] Executed block                               module=state height=6 validTxs=0 invalidTxs=0
I[04-09|02:47:18.141] Committed state                              module=state height=6 txs=0 hash=BB3D7113AE674C471BCB373ECF39B315F700A597FD568E0F6B667E1F5074FD47
```

- 启动ethermint 
- 
```
ethermint --datadir ./ethermint --rpc --rpcaddr=0.0.0.0 --ws --wsaddr=0.0.0.0 --rpcapi eth,net,web3,personal,admin

INFO [04-09|10:47:07] Starting peer-to-peer node               instance=ethermint/v1.6.7-stable/linux-amd64/go1.9.3
INFO [04-09|10:47:07] Allocated cache and file handles         database=/home/parallels/work/ethermint_chain/ethermint/ethermint/chaindata cache=128 handles=1024
WARN [04-09|10:47:07] Upgrading chain database to use sequential keys 
INFO [04-09|10:47:07] Initialised chain configuration          config="{ChainID: 15 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: <nil> EIP155: 0 EIP158: 0 Metropolis: <nil> Engine: unknown}"
WARN [04-09|10:47:07] Ethash used in fake mode 
WARN [04-09|10:47:07] Upgrading db log bloom bins 
INFO [04-09|10:47:07] Database conversion successful 
INFO [04-09|10:47:07] Bloom-bin upgrade completed              elapsed=93.384µs
INFO [04-09|10:47:07] Initialising Ethereum protocol           versions="[63 62]" network=1
INFO [04-09|10:47:07] Loaded most recent local header          number=0 hash=2b72f9…d468ac td=64
INFO [04-09|10:47:07] Loaded most recent local full block      number=0 hash=2b72f9…d468ac td=64
INFO [04-09|10:47:07] Loaded most recent local fast block      number=0 hash=2b72f9…d468ac td=64
INFO [04-09|10:47:07] Starting P2P networking 
INFO [04-09|10:47:07] RLPx listener up                         self="enode://7ffbe6a54ec88ec44cc52409cec7d60e20121a0205a8622912646c093b1ea64c39925390eb3e1c162d367aa9c355268a569e36444a537884d080e8d5a8338fc8@[::]:30303?discport=0"
INFO [04-09|10:47:07] Waiting for tendermint endpoint to start err="Post http://localhost:46657/status: dial tcp 127.0.0.1:46657: getsockopt: connection refused"
INFO [04-09|10:47:07] HTTP endpoint opened: http://0.0.0.0:8545 
INFO [04-09|10:47:07] WebSocket endpoint opened: ws://0.0.0.0:8546 
INFO [04-09|10:47:07] IPC endpoint opened: /home/parallels/work/ethermint_chain/ethermint/geth.ipc 
INFO [04-09|10:47:09] Starting ABCIServer                      module=abci-server impl=ABCIServer
INFO [04-09|10:47:09] Waiting for new connection...            module=abci-server
INFO [04-09|10:47:10] Accepted a new connection                module=abci-server
INFO [04-09|10:47:10] Waiting for new connection...            module=abci-server
INFO [04-09|10:47:10] Accepted a new connection                module=abci-server
INFO [04-09|10:47:10] Waiting for new connection...            module=abci-server
INFO [04-09|10:47:10] Accepted a new connection                module=abci-server
INFO [04-09|10:47:10] Waiting for new connection...            module=abci-server
INFO [04-09|10:47:13] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=419.72µs mgasps=0.000 number=1 hash=dcc7b9…e2ff0c
INFO [04-09|10:47:14] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=342.37µs mgasps=0.000 number=2 hash=32ec1a…f21365
INFO [04-09|10:47:15] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=428.186µs mgasps=0.000 number=3 hash=5909f1…c05090
INFO [04-09|10:47:16] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=428.889µs mgasps=0.000 number=4 hash=f44a8d…f11575
INFO [04-09|10:47:17] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=432.626µs mgasps=0.000 number=5 hash=f7775d…1b4c71
INFO [04-09|10:47:18] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=343.954µs mgasps=0.000 number=6 hash=bb3d71…74fd47
INFO [04-09|10:47:19] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=431.598µs mgasps=0.000 number=7 hash=a4dbc9…006f4e
INFO [04-09|10:47:20] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=469.884µs mgasps=0.000 number=8 hash=3a9f69…14bfce

```
在ethermint启动前，tendermint窗口会一直打印如下log

```
abci.socketClient failed to connect to tcp://127.0.0.1:46658
```
在ethermint启动之后，可以看到tendermin和ehtermint建立的连接
后面看到已经有区块生成，这样一个Ethermint节点就启动了

## 与Ethermint节点交互
因为ethermint是基于以太坊的，因此可以使用以太坊geth工具连接到Ethermint，执行如下命令，连接到Ethermint

```
geth attach http://localhost:8545
Welcome to the Geth JavaScript console!

instance: ethermint/v1.6.7-stable/linux-amd64/go1.9.3
coinbase: 0x7eff122b94897ea5b0e2a9abf47b86337fafebdc
at block: 141 (Mon, 09 Apr 2018 10:49:35 CST)
 datadir: /home/parallels/work/ethermint_chain/ethermint
 modules: admin:1.0 eth:1.0 net:1.0 personal:1.0 rpc:1.0 web3:1.0
 > 
```
下面从账户操作、部署合约以及与合约交互等几个操作是如何与节点进行交互的。

- 查看账户
```
> eth.accounts
["0x7eff122b94897ea5b0e2a9abf47b86337fafebdc"]

```
前面已经说明默认配置了一个账户，可以从./ethermint/keystore中看到：
```
parallels@ubuntu:~/work/ethermint_chain/ethermint/keystore$ ls
UTC--2016-10-21T22-30-03.071787745Z--7eff122b94897ea5b0e2a9abf47b86337fafebdc
```
- 查看已经产生的区块高度
```
> eth.blockNumber
568
```
- 解锁账户
在部署合约或者发起交易前，需要先解锁账户
```
 > web3.personal.unlockAccount("0x7eff122b94897ea5b0e2a9abf47b86337fafebdc", "1234", 100000)
true
```
第一个参数是账户，第二参数世密码，第三个参数是账户的保持解锁时间，单位是秒

- 部署合约  
 一个简单的合约代码，主要设计的存取一个uint数据，代码如下

```
pragma solidity ^0.4.0;
 
contract SimpleStorage {
    uint storedData;
 
    function set(uint x) public {
        storedData = x;
    }
 
    function get() public constant returns (uint) {
        return storedData;
    }
}
```
使用Remix进行合约编译，编译完成后Contract detail有构建合约类和合约部署代码可以直接拿来用。

- 部署合约

```
var simplestorageContract = web3.eth.contract([{"constant":false,"inputs":[{"name":"x","type":"uint256"}],"name":"set","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"get","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"}]);

var simplestorage = simplestorageContract.new(
   {
     from: web3.eth.accounts[0], 
     data: '0x6060604052341561000f57600080fd5b60d38061001d6000396000f3006060604052600436106049576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806360fe47b114604e5780636d4ce63c14606e575b600080fd5b3415605857600080fd5b606c60048080359060200190919050506094565b005b3415607857600080fd5b607e609e565b6040518082815260200191505060405180910390f35b8060008190555050565b600080549050905600a165627a7a723058205d8a5d19a07f5b8f9672432432fee6d2661eb120103442f96b4ae5d80886bfd30029', 
     gas: '4700000'
   }, function (e, contract){
    console.log(e, contract);
    if (typeof contract.address !== 'undefined') {
         console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
    }
 })
```

在geth命令行窗口执行以上代码进行合约部署，会在ethermint窗口看到已经提交了一个合约
```
INFO [04-09|14:31:48] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=379.11µs  mgasps=0.000 number=177 hash=3580df…dbba77
INFO [04-09|14:31:48] Submitted contract creation              fullhash=0xb09c872c6c72b0aedcad8ee8a50d0a04bd14c2ce02da2d2c2950b5ff865a5c59 contract=0xb6b29ef90120bec597939e0eda6b8a9164f75deb
INFO [04-09|14:31:49] Imported new chain segment               blocks=1 txs=1 mgas=0.109 elapsed=620.595µs mgasps=175.406 number=178 hash=110112…a5febd
```
在tendermint命令行窗口会看到如下log:
```
I[04-09|06:31:48.591] Committed state                              module=state height=177 txs=0 hash=3580DF3BC1C33F80A5B37A3B629245E5F42083028B9B752BAAAE5B5E89DBBA77
I[04-09|06:31:49.611] Executed block                               module=state height=178 validTxs=1 invalidTxs=0
I[04-09|06:31:49.612] Committed state                              module=state height=178 txs=1 hash=11011286ECAB981CBB74665F3985E07840FA8CAA2665BB4D1D1BFFA1FEA5FEBD

```

在geth窗口会看到如下log,意味着合约提交到区块了：
```
> null [object Object]
Contract mined! address: 0xb6b29ef90120bec597939e0eda6b8a9164f75deb transactionHash: 0xb09c872c6c72b0aedcad8ee8a50d0a04bd14c2ce02da2d2c2950b5ff865a5c59
```
根据交易反馈的hash取得对应区块的信息：
从区块信息上，该合约已经被成功的部署到链上，区块高度是178
```
> eth.getTransaction('0xb09c872c6c72b0aedcad8ee8a50d0a04bd14c2ce02da2d2c2950b5ff865a5c59')
{
  blockHash: "0x11011286ecab981cbb74665f3985e07840fa8caa2665bb4d1d1bffa1fea5febd",
  blockNumber: 178,
  from: "0x7eff122b94897ea5b0e2a9abf47b86337fafebdc",
  gas: 4700000,
  gasPrice: 18000000000,
  hash: "0xb09c872c6c72b0aedcad8ee8a50d0a04bd14c2ce02da2d2c2950b5ff865a5c59",
  input: "0x6060604052341561000f57600080fd5b60d38061001d6000396000f3006060604052600436106049576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806360fe47b114604e5780636d4ce63c14606e575b600080fd5b3415605857600080fd5b606c60048080359060200190919050506094565b005b3415607857600080fd5b607e609e565b6040518082815260200191505060405180910390f35b8060008190555050565b600080549050905600a165627a7a723058205d8a5d19a07f5b8f9672432432fee6d2661eb120103442f96b4ae5d80886bfd30029",
  nonce: 0,
  r: "0xc21c1bc7b16316e8346a5a06b436d6ce722cda330549b0db9b8f00e6c9dbe8a6",
  s: "0x302aaaa5027401a22eba3bdebfeeb345f9862f237b5f12c3bf143ba2e6ba0961",
  to: null,
  transactionIndex: 0,
  v: "0x42",
  value: 0
}

```
- 与合约进行交互  
根据合约类和地址取得合约实例：
```
var store = simplestorageContract.at("0xb6b29ef90120bec597939e0eda6b8a9164f75deb")
```
往合约上存储一段数据，提交后需要经过一个区块的打包验证，代码如下：
```
store.set.sendTransaction(100, {from:eth.accounts[0]})
"0x6f06e959b798751fb3f90b2d7e5ba28fc1cc33b31701df42cdbbf4f99e4dfc3d"
```
从合约上获取数据
```
> store.get.call()
100
```
通过交易的方式数据看不到数据返回显示，后面继续调查。
```
> store.get.sendTransaction(a, {from:eth.accounts[0]})
```
这样就完成了合约的创建、部署与交互了。

## 参考资料：
- Ethermint GetStarted: http://ethermint.readthedocs.io/en/master/getting-started/getting-started.html
- 连接venus测试网络http://ethermint.readthedocs.io/en/master/testnets/venus.html#
- ethermint私有网络：https://github.com/b00f/ethermint-private-network
- Remix:https://ethereum.github.io/browser-solidity/
- tendermint:https://tendermint.com/