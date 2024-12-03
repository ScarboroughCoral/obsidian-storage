# ethers.js

  

[](https://docs.ethers.org/v6/)

## 概念

  
  

### Provider

  
  

#### ethers中的provider是对eth网络只读连接的抽象。可以读取区块链状态比如账户、区块、交易信息、event logs、通过call执行只读代码

  
  

### Signer

  
  

#### 封装了所有对账户的操作。

  
  

##### 账户有一个私钥存在某个地方

  
  

###### 要么通过使用wallet存在内存中

  
  

###### 要么通过IPC层保护（比如metamask通过浏览器插件代理了网页相关操作，保护私钥不被网页直接访问，只有用户允许的操作才会被执行

  
  

### Contract

  
  

#### 是部署到区块链中的程序，包含代码和分配的可读写存储区

  
  

##### 当连接Provider后可以做读操作

  
  

##### 当连接Signer后可以做修改操作

  
  

### Transaction

  
  

#### 对于任何对区块链上状态修改的都需要transaction

  
  

##### 需要fee来做计算更新存储信息操作

  
  

##### transaction回滚也需要支付fee，验证者需要尝试运行交易来确认回滚以及存储失败回滚原因

  
  

##### 分类

  
  

###### ether转账

  
  

###### 部署合约

  
  

###### 修改合约状态

  
  

### Receipt

  
  

#### 当transaction提交后，会在memory pool 中排队等待Validator确认是否处理该交易

  
  

#### transaction的修改只会在打包到区块链中时执行一次，同时会产生receipt，receipt中包含详细的transaction信息，包含所在区块、实际使用的fee、gas、产生的events、是否成功/回滚

  
  

### 单位

  
  

#### wei

  
  

##### 最小单位

  
  

###### 以Wei Dai命名，比特币前身b-money创建者

  
  

#### gwei

  
  

##### 使用最多的单位，gas费一般使用gwei单位

  
  

####

  

![208](/Users/limingyuebj/Documents/208.png)

  

### ENS

  
  

#### Ethereum Name Service。有点像dns，用于将方便阅读的名字比如"alice.eth"映射到eth地址/加密货币地址/内容hash/元数据。

  
  

## 应用

  
  

### 连接eth

  
  

#### 连接metamask/其他注入方式provider

  
  

##### metamask插件会在window下注入对象

  
  

###### 对eth的只读权限（provider

  
  

###### 通过私钥进行写操作授权（signer

  
  

###### 当进行需要授权的写操作或者请求私钥地址时，metamask会弹窗让用户确认

  
  

#####

  

![172](/Users/limingyuebj/Documents/172.png)

  

#### 连接自定义rpc，比如开发节点或者第三方服务

  
  

##### 直接用JsonRpcProvider（遵循link-jsonrpc 协议）

  
  

##### JsonRpCProvider.getSigner连接账户

  
  

######

  

![185](/Users/limingyuebj/Documents/185.png)

  

### 单位转换

  
  

####

  

![218](/Users/limingyuebj/Documents/218.png)

  

### 与区块链交互

  
  

#### 读状态

  
  

##### 前提：需要Provider

  
  

##### 当前账户状态

  
  

##### 历史日志

  
  

##### 合约代码

  
  

##### 相关操作

  
  

######

  

![235](/Users/limingyuebj/Documents/235.png)

  

#### 发送transaction

  
  

##### 前提：需要Signer

  
  

##### 比如向MetaMask发送请求，metamask中可以选择同意或拒绝操作

  
  

#####

  

![300](/Users/limingyuebj/Documents/300.png)

  

#### 合约

  
  

##### 是一个meta-class，运行时基于传入的ABI动态生成

  
  

##### 创建合约

  
  

######

  

![330](/Users/limingyuebj/Documents/330.png)

  

##### abi

  
  

###### 与以太坊网络交互需要二进制

  
  

###### 多种表示形式

  
  

- solidity compiler使用JSON转储

  

- 便于人类阅读的 solidity 函数签名

  

- "function decimals() view returns (string)"

  

##### 只读方法。pure 和 view

  
  

######

  

![335](/Users/limingyuebj/Documents/335.png)

  

##### 状态修改方法

  
  

######

  

![340](/Users/limingyuebj/Documents/340.png)

  

######

  

![343](/Users/limingyuebj/Documents/343.png)

  

##### 监听事件

  
  

######

  

![348](/Users/limingyuebj/Documents/348.png)

  

##### 查询历史事件

  
  

###### 如果查询大范围的区块链可能会非常非常慢甚至报错或者未提示的截断，由其后端决定

  
  

##### 签名信息

  
  

######

  

![357](/Users/limingyuebj/Documents/357.png)

  

## 问题

  
  

### fee和gas有区别吗？

  
  

### Provider、metamask钱包、ethers的关系

  
  

#### Provider是eth的连接提供者，metamask有内置的provider，ethers可以通过使用metamask内置的provider对eth进行只读查询

  
  

### 单位之间的换算

  
  

## 学习资料

  
  

### https://www.wtf.academy/docs/ethers-101/

  
  

### https://docs.ethers.org/v6/getting-started/#starting-blockchain