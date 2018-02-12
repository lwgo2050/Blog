---
title: 比特股Web钱包搭建以及接入到私有链
tags: [比特股,bitshares,钱包,wallet]
---

BitShares-UI是比特股轻钱包，BitShares API 由 witness_node 程序提供。比特股钱包有两种模式：钱包模式和账号模式，后面再具体阐述这两种模式的区别


# 1.依赖环境配置
BitShares-UI 依赖于 Node.js v6 以上版本，通过nvm可以很方便的管理node版本，执行如下命令
安装NVM以及V6:

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
nvm install v6
```
切换到v6:

```
nvm use v6
```
这个查看node是否已经切换到V6：

```
$ node -v 
v6.12.3
```
本地切换只针对当前命令行窗口有效，新打开命令行，需要重新切换v6

# 2.代码下载及编译
## 获取项目源代码

```
git clone https://github.com/bitshares/bitshares-ui.git
cd bitshares-ui
```
## 安装项目依赖

```
npm install
```

## 编译及启动服务器

```
npm start
```
成功的话，会看到如下提示：

```
webpack: Compiled successfully.
```
此时打开浏览器输入如下地址就可以看到web钱包已经运行起来：

```
http://localhost:8080
```
默认接入的比特股公有链，这个和比特股的官方钱包是一样的，它是一个钱包同时是一个交易所。
目前的比特股交易所都是通过这种方式搭建的，主要用来上线一些数字货币以及通过注册账户获取账户的交易手续费来盈利，大致的步骤如下：
- 搭建见证人节点
- 搭建比特股钱包
- 搭建水龙头服务

具体手续费的分配比例与比特股的账户引荐人制度有关系，以后在具体阐述。

# 3.钱包接入到私有链
上述步奏已经完成了比特股钱包的搭建，并接入到公有链中，那么如何将钱包接入的自己搭建的私有链中呢，其实很简单，在当前打开的钱包中点击Settings->Access 点击Add node,如下图所示：
![image](https://raw.githubusercontent.com/wangmaodong/Blog_Markdown/master/images/add_node.png)

添加完成之后选中新建的节点点击active,即激活使用本地节点了，如下图所显示：
![image](https://raw.githubusercontent.com/wangmaodong/Blog_Markdown/master/images/save_node.png)

点击Exlpore,看以看到本地节点生成的区块，可以通过cli-walletz执行转账，验证区块是否将建议打包，可以多转几次：

```
unlocked >>> transfer nathan alpha 2 BTS "here is some cash" true
```
从下图可以看到，下次区块产生的时候，交易已经被验证了：
![image](https://raw.githubusercontent.com/wangmaodong/Blog_Markdown/master/images/success.png)

从而验证钱包已经接入到私有链了，账户注册需要搭建水龙头服务，后面待续。
