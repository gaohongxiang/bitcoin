
项目依赖
- 一台cpu好点的电脑，一个2T的SSD固态硬盘（挖矿靠cpu，存储读写数据靠ssd固态硬盘。目前比特币全节点数据608G，electrumx数据94G，2T预留未来空间）

## 准备工作

由于比特币全节点数据非常大，从头开始同步的话会很慢，所以，我们可以提前下载别人打包好的数据，然后只需要同步后面新产生的数据就好了。这样会节省很多时间。

打包好的比特币节点数据（截止到2022.8）
- https://pan.quark.cn/s/4c3b98e42c1f#/list/share

在开通夸克网盘会员的情况下，大概9小时下载完毕。

数据下载完成后，用`7-zip`解压，数据备用。

>解压后比特币数据包含`blocks`、`chainstate`两个文件夹。

## 同步比特币节点

### 1、下载bitcoin core并配置

官网：https://bitcoincore.org/en/download/

根据自己的操作系统下载对应压缩版本，然后解压，并移动到自己的移动硬盘中

```
进入解压缩文件，创建数据存储文件夹，比如 bitcoin_data，后续所有的数据都存在这个文件夹里。
```

运行bitcoin core，初次运行要选择比特币数据存锤位置，这里存储在自己的移动硬盘中。即刚创建的数据文件夹`bitcoin_data`。

![选择数据存储位置](https://github.com/gaohongxiang/atomicals/assets/33713367/93b28d24-4c32-40a7-a1fb-f1df5df62ab6)


找到`设置-选项`，根据下图配置

![bitcoin core 配置](https://github.com/gaohongxiang/atomicals/assets/33713367/48b4ef91-387e-446e-b0f1-db4e030e992a)

>注：项目有两个`bitcoin.conf`配置文件，在项目根目录下的那个是示例，真正用到的在数据目录下，即`bitcoin_data`目录下。不要配置错了。可以直接通过bitcoin core客户端的选项里打开配置文件

`bitcoin.conf`配置文件配置内容如下

修改rpcuser、rpcpassword字段，自己设置，后面electrumx连接比特币节点要用到。

```
# 指示比特币节点运行为完整节点（full node），允许其他节点连接并获取区块链数据
server=1
# 将比特币节点设置为后台运行的守护进程，而不是在命令行或图形界面中交互式地运行
daemon=1
# 启用事务索引（transaction index），允许比特币节点通过事务哈希快速查找和检索特定的交易。当该选项设置为 1 时，比特币节点会从第一个区块开始索引所有的交易
txindex=1
# 配置 ZeroMQ 发布原始区块数据的地址和端口。ZeroMQ 是一种消息传递库，用于在比特币网络中实时传输数据。
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333

# 用于比特币节点的远程过程调用（RPC）身份验证的用户名和密码
rpcuser=<自行替换>
rpcpassword=<自行替换>
# 更安全的验证方式是用rpcauth，如下
# rpcauth=electrumx:XXX

# 添加大陆节点确保稳定极速同步
# 大陆节点查找：https://bitnodes.io/nodes/?q=China
addnode = 120.79.71.72:8333
addnode = 175.27.222.132:8333
addnode = 42.231.189.65:8333
addnode = 194.156.99.41:10001
addnode = 124.222.39.240:8333
addnode = 27.148.206.140:8333

# 通过rpcbind=0.0.0.0和rpcallowip的设置灵活控制IP地址对节点 RPC 接口的访问，以增加安全性。
# 指定比特币节点监听的 RPC 绑定地址。设置为 0.0.0.0 表示节点将接受来自所有网络接口的连接请求。换句话说，它允许从任意 IP 地址访问节点的 RPC 接口
rpcbind=0.0.0.0
# rpcallowip指定允许访问节点 RPC 接口的 IP 地址。
# 设置为 127.0.0.1 表示允许本地主机访问节点的 RPC 接口，即只有在同一台机器上运行的程序可以连接和调用节点的 RPC
rpcallowip=127.0.0.1
# 设置为 192.168.0.0/16 表示允许具有以 192.168 开头的局域网 IP 地址的设备连接和调用节点的 RPC 接口。这通常包括您的本地网络中的设备
rpcallowip=192.168.0.0/16
# 设置为 172.0.0.0/8 表示允许具有以 172 开头的默认 Docker 容器网络 IP 地址的设备连接和调用节点的 RPC 接口
rpcallowip=172.0.0.0/8
```

配置好后重启bitcoin core生效。

### 2、移动打包好的数据

将第一步下载的打包好的比特币数据移动到`bitcoin_data`文件夹下，替换掉`blocks`和`chainstate`这两个文件夹。

![image](https://github.com/gaohongxiang/atomicals/assets/33713367/cec0eae5-f0f8-42eb-b19c-cbe95d68f973)

### 3、同步比特币数据

再次打开bitcoin core软件，数据开始同步，可以看到，我们下载好的数据不需要重新同步了，只需要同步剩下的数据就好了，耐心等待，需要几个小时才能同步完。同步完成后就有了比特币全节点数据！

## 参考
- 如何搭建 Bitcoin Core 比特币全节点：https://yishi.io/how-to-run-bitcoin-full-node/
- 如何极速同步Bitcoin 全节点：https://twitter.com/weixiaoqvq/status/1716014155730465267