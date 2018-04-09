---
title: 基于以太坊cpp_ethereum客户端的私有链搭建
subtitle: eth 
tags: [区块链,以太坊]
date: 2018-4-6
---

## 前言
 以太坊提供了多种语言的客户端，其中以go、C++、Party三种语言的客户端最为流行。目前以太坊以go语言的客户端为官方版本，官方网站和github上的资料都是以go语言的客户端为主，而对其他语言客户端的资料和wiki说明都是少之又少。利用官方go_ethereum的客户端创建私有链或者私有测试网络都是十分便捷，这里不多赘述，网上资料一大把。本问主要针对的是C++客户端（cpp_ethereum）去搭建私有节点测试网络，并在这个网络上执行挖矿和发起交易等常规操作。
 
## 客户端环境准备
为了以太坊C++客户端搭建私有链，需要准备好以太坊客户端程序和用于连接客户端的命令行工具（ethconsole）,以太坊C++客户端没有像go语言客户端那样默认集成了命令行（go console），因为需要用ethconsole去连接客户端.本文的测试环境都是基于unbuntu16.04 64位的机器去测试的。
<!-- more-->
### cpp_ethereum 客户端安装
cpp_etherem 客户端安装可以采用二进制文件安装和代码下载编译安装，为了了解cpp_ethereum 的运作流程，推荐编译安装。
#### 二进制包安装
执行一下命令即可：
```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install cpp-ethereum

```

#### 编译安装
克隆官方源码：
```
git clone --recursive https://github.com/ethereum/cpp-ethereum.git
```

安装编译依赖，默认本机已经安装了cmake,并至少保证在cmake 3.4.3以上
- 检查cmake 版本
```
cmake --version
cmake version 3.5.1
```
- 安装leveldb
sudo apt-get install libleveldb-dev

编译二进制客户端
```
cd cpp-ethereum        # 进入源文件目录
mkdir build; cd build  # 创建编译目录
cmake ..               # 配置源码编译
cmake --build .        # 编译所有的目标文件
```
编译完成之后会在目标build 文件夹中看到二进制可执行程序(eth\ethvm\ethkey)和库,eth也就是我们的客户端程序了

## ethereum-console安装
官方提供的ethereum-console是用javascript编写的,请首先检查本机安装了nodejs和npm,没有的话请自行搜索安装
- 命令行安装
```
npm install -g ethereum-console
```
经过测试npm安装的ethconsole不是最新版本,里面的方法不能用,最好用官方源代码进行安装:
```
git clone https://github.com/ethereum/ethereum-console.git
cd ethereum-console
npm install
```
ethereum中的main.js是一个执行的命令行程序(将ethereum-console中的拷贝到npm 安装的ethconsole目录亦可)
## 单节点环境
### 创建config.json文件
```
{
	"sealEngine": "Ethash",
	"params": {
		"accountStartNonce": "0x00",
		"homesteadForkBlock": "0x00",
		"daoHardforkBlock": "0x00",
		"EIP150ForkBlock": "0x00",
		"EIP158ForkBlock": "0x00",
		"byzantiumForkBlock": "0x00",
		"networkID" : "0x01011",
		"chainID": "0x0101",
		"maximumExtraDataSize": "0x20",
		"tieBreakingGas": false,
		"minGasLimit": "0x1388",
		"maxGasLimit": "7fffffffffffffff",
		"gasLimitBoundDivisor": "0x0400",
		"minimumDifficulty": "0x020000",
		"difficultyBoundDivisor": "0x0800",
		"durationLimit": "0x0d",
		"blockReward": "0x4563918244F40000"
	},
	"genesis": {
		"nonce": "0x0000000000000042",
		"difficulty": "0x000004000",
		"mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"author": "0x0000000000000000000000000000000000000000",
		"timestamp": "0x00",
		"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"extraData": "0x11bbe8db4e347b4e8c937c1c8370e4b5ed33adb3db69cbdb7a38e1e50b1b82fa",
		"gasLimit": "0x099999999990"
	},
	"accounts": {
		"0000000000000000000000000000000000000001": { "precompiled": { "name": "ecrecover", "linear": { "base": 3000, "word": 0 } } },
		"0000000000000000000000000000000000000002": { "precompiled": { "name": "sha256", "linear": { "base": 60, "word": 12 } } },
		"0000000000000000000000000000000000000003": { "precompiled": { "name": "ripemd160", "linear": { "base": 600, "word": 120 } } },
		"0000000000000000000000000000000000000004": { "precompiled": { "name": "identity", "linear": { "base": 15, "word": 3 } } },
		"0000000000000000000000000000000000000005": { "precompiled": { "name": "modexp", "startingBlock" : "0x2dc6c0" } },
		"0000000000000000000000000000000000000006": { "precompiled": { "name": "alt_bn128_G1_add", "startingBlock" : "0x2dc6c0", "linear": { "base": 500, "word": 0 } } },
		"0000000000000000000000000000000000000007": { "precompiled": { "name": "alt_bn128_G1_mul", "startingBlock" : "0x2dc6c0", "linear": { "base": 40000, "word": 0 } } },
		"0000000000000000000000000000000000000008": { "precompiled": { "name": "alt_bn128_pairing_product", "startingBlock" : "0x2dc6c0" } },
		"3282791d6fd713f1e94f4bfd565eaa78b3a0599d": {
		"balance": "1337000000000000000000"
		},
		"17961d633bcf20a7b029a7d94b7df4da2ec5427f": {
		"balance": "229427000000000000000"
		},
		"493a67fe23decc63b10dda75f3287695a81bd5ab": {
		"balance": "880000000000000000000"
		}
	}
}

```
config.json文件中主要配置了网络的一些基本参数\创世块\创世账户的一些情况
### 节点启动
创建块存储目录
```
mkdir data
```
执行如下命令启动节点
```
eth --config config.json --db-path ./data --listen 30001 --port 30001 --no-discovery -v 9
```
见到如下log 输出即节点启动成功
```
cpp-ethereum, a C++ Ethereum client
⧎ ℹ  03:59:25 PM.525|eth  Id: ##494833a4…
...  03:59:25 PM.540|eth  Opened blockchain DB. Latest: #4e9ad5dd… (rebuild not needed)
⧫ ◎  03:59:25 PM.543|eth  startedWorking()
cpp-ethereum 1.3.0
  By cpp-ethereum contributors, (c) 2013-2018.
  See the README for contributors and credits.
Mining Beneficiary: 00000000-0000-0000-0000-000000000000 - 03c6013d4e1bf5a6f0da04a0d5f3026eba50a581
  ℹ  03:59:27 PM.036|p2p  UPnP device not found.
⧎ ℹ  03:59:27 PM.036|p2p  p2p.started id: ##494833a4…
Node ID: enode://494833a461270a10b9ff8163eaecfa4eba73c55c9d49f95e1c71d3d8337ce4201bdaba97541174575b12a8d964e28511827ba5e17dad2440f9272dfef2be3697@0.0.0.0:0
JSONRPC Admin Session Key: LApZFQPWF/s=
```

这样一个节点就驱动成功了,是不是很简单,但是你可能会很疑惑,那我怎么去挖矿,怎么区转账的,怎么区转账呢?这个时候ethconsole就派上用场了,cpp-ethereum客户端没有默认继承websocker-server/或者http-server,而是认为这些不是节点的一部分,只提供了UnixSocket和Windows的命名管道通信,应当由第三方来自由的开发这些服务.

### 与客户端节点交互
节点启动之后默认在区块存储目录会生成一个geth.ipc的文件,可以通过这个文件与节点建立通信
进入ethereum-console源文件目录执行如下命令
```
node main.js ../privatechain/data/geth.ipc
```
输出如下信息则代表已经与节点建立了连接:
```
ETHEREUM CONSOLE
Connecting to node at ../../eth-chain/privatechain/data/geth.ipc ...
Connection successful!

ΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞ
Network: PRIVATE
Current block: 0 (January 1st 1970, 08:00:00)
ΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞΞ

> 
```
下面将会通过在节点是进行简单的一些操作:创建账户\挖矿\交易等
- 创建账户
执行如下命令,创建一个账户
```
> web3.eth.personal.newAccount('123')
'0xf81C8113F4463BD4051a41E479Bf117c1B406fe9'
```
查看账户余额:
```
> web3.eth.getBalance('0xf81C8113F4463BD4051a41E479Bf117c1B406fe9')
'0'
```
可以看到账户余额为0

- 挖矿
设置挖矿账户
```
> web3.miner.setEtherbase('0xf81C8113F4463BD4051a41E479Bf117c1B406fe9')
true
```
执行挖矿
```
> web3.miner.start(1)
true
```
可以看到eth客户端程序中有如下的log输出
```

```
查看挖矿账户余额
```

```

- 交易
新建一个账户
```
> web3.eth.personal.newAccount('123')
'0x004e2088600aCCb78F0A5Fd25a96b7d97841D5cE'
```
利用挖矿账户的余额向新建账户

## 多节点私有链搭建
上一节中我们已经建立了一个单节点,创建多节点的私有网络就是利用利用P2P将这些节点连接起来
- 新建一个新的节点
按照上节的内容重新建立一个节点
```
mkdir privatechain2
eth --config config.json --db-path ./data --listen 30002 --port 30002 --no-discovery -v 9
```
如果是在个一台机器上建立多个节点需要将listen 和port端口修改成节点1不一样,如果在多台机器上不需要进行此设置
查看节点1 nodeinf:
```
{ enode: 'enode://3d07256b98eb04f345a8d24f6c6c5a6e2f4275c01f665dc1e6f83a09ac3b7edc7dff457c466afdd96181d5213214937d46dfcfbb0775d1dfa275154e01153458@0.0.0.0:0',
  id: '3d07256b98eb04f345a8d24f6c6c5a6e2f4275c01f665dc1e6f83a09ac3b7edc7dff457c466afdd96181d5213214937d46dfcfbb0775d1dfa275154e01153458',
  ip: '0.0.0.0',
  listenAddr: '0.0.0.0:0',
  name: 'eth/v1.3.0/Linux/g++/Interpreter/RelWithDebInfo/2e0dacbc*/',
  ports: { discovery: 0, listener: 0 },
  protocols: { eth: {} } }

```
在节点2通过addPeer添加
```
> web3.admin.addPeer('enode://3d07256b98eb04f345a8d24f6c6c5a6e2f4275c01f665dc1e6f83a09ac3b7edc7dff457c466afdd96181d5213214937d46dfcfbb0775d1dfa275154e01153458@127.0.0.1:30001')
true

```
在节点1上查看是否与节点1是否连接成功
```
> web3.admin.peers()
[ { caps: [ 'eth/62', 'eth/63' ],
    id: '70563c5dd5837a1d3989673835e0e8bf49d6f5270d7c587e68237e7070fa84ba990443451480acb12aea810079196aeb369ede0d5c4c4b9b01d58854a405b705',
    lastPing: 0,
    name: 'eth/v1.3.0/Linux/g++/Interpreter/RelWithDebInfo/2e0dacbc*/',
    network: { remoteAddress: '127.0.0.1:30002' },
    notes: { ask: 'Nothing', manners: 'nice', sync: 'holding & needed' } } ]
```
从上面可以看出已经连接成功了,按照同样的方式可以加入过个节点构建一个多节点的私有网络

## 参考资料
- cpp-ethereum客户端官方资料：http://www.ethdocs.org/en/latest/ethereum-clients/cpp-ethereum/index.html
- cpp-ethereum官方github: https://github.com/ethereum/cpp-ethereum
- ethereum-console https://github.com/ethereum/ethereum-console

