铸造代币有三种参与方式
1. 通过代打平台铸造代币
2. 通过客户端cli + 公共RPC节点 铸造代币
3. 通过客户端cli + 私有RPC节点 铸造代币


操作难易程度：1 > 2 > 3
```
方式1只需要打开代打平台官网，连接钱包就可以打
方式2需要有一定编程基础
方式3不仅需要编程基础，还需要硬件设备支持，比如cpu、固态硬盘等
```

铸造成功率：3 > 2 > 1
```
方式1受限于代打平台的稳定性和公共rpc的可靠性。往往热门项目铸造时代打平台承载不了大流量导致官网打不开，同样流量大时公共rpc节点也会挂掉
方式2受限于大流量导致公共rpc挂掉
方式3基本不受限
```

# 一、通过代打平台铸造代币

- wizz官网:https://wizz.cash/mining (推荐，更新比较快。mint速度非常快。钱包的mint入口进入)
- satsx官网：https://www.satsx.io/atomicals
- atom市场：https://atomicalmarket.com/inscribe 

# 二、通过客户端cli + 公共RPC节点 铸造代币

目前有两个客户端，一个是atomicals协议官方js版本的`atomicals-js`，另一个是推特用户AurevoirXavier写的rust版本的`atomicalsir`。两个用哪个都行，rust版本不是纯净版，目前依赖atomicals-js，引擎可以选rust，速度会比js快。

## 2.1 准备
- git
- node
- rust

>注：命令都是在终端中执行的，win下的终端如`cmd`、`PowerShell`都需要右键以管理员身份打开，否则容易权限不足

### 2.1.1 安装git

https://git-scm.com/downloads

终端输入命令查看node版本
```
git -v
```

### 2.1.2 安装node

https://nodejs.org/en

终端输入命令查看node版本
```
node -v
```

### 2.1.3 安装rust
```
# 安装rustup（rust的包管理工具）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# 安装nightly版rust（atomicalsir使用的此版本）
rustup install nightly
# 查看版本
rustc --version
```

## 2.2 使用atomicals-js（js版cli）

### 2.2.1 拉取并编译项目
```
# 创建工作目录
mkdir atomicals && cd atomicals

# github 克隆
git clone https://github.com/atomicals/atomicals-js.git

# 进入项目目录
cd atomicals-js

# 安装 yarn
npm install -g yarn

# 安装依赖
yarn install

# 编译（完成后会有一个dist文件夹）
yarn build
```

### 2.2.2 创建钱包
```
yarn cli wallet-init
```
执行创建钱包命令后会在当前目录下会生成 wallet.json 钱包信息，注意保存好 wallet.json 钱包文件
>说明：创建钱包后，会自动生成2个地址，Primary Address和Funding Address，其中Primary是用于接收Atomicals生态资产的，比如ARC20代币、图片NFT等，Funding是用于铸造的付款钱包，可以提前充值进去。

##### 准备多钱包（导入）

一个钱包受限于gas高低及链上出块速度。往往需要准备多个钱包同时mint。将创建的多个钱包存入钱包文件的`imported`字段即可

wallet.json结构如下。其中`p1 f1`、`p2 f2`是导入的钱包
```
{
  "phrase": "...",
  "primary": {
    "address": "...",
    "path": "m/86'/0'/0'/0/0",
    "WIF": "..."
  },
  "funding": {
    "address": "...",
    "path": "m/86'/0'/0'/1/0",
    "WIF": "..."
  },
  "imported": {
    "p1": {
      "address": "...",
      "path": "m/86'/0'/0'/0/0",
      "WIF": "..."
    },
    "f1": {
      "address": "...",
      "path": "m/86'/0'/0'/1/0",
      "WIF": "..."
    }
    "p2": {
      "address": "...",
      "path": "m/86'/0'/0'/0/0",
      "WIF": "..."
    },
    "f2": {
      "address": "...",
      "path": "m/86'/0'/0'/1/0",
      "WIF": "..."
    }
  }
}
```

### 2.2.3 常用命令

#### 铸造FT币命令
```
# 使用默认钱包
yarn cli mint-dft atom --satsbyte 30
# 使用导入钱包，钱包f1付款，钱包p1接收铭文
yarn cli mint-dft atom --funding 'f1' --initialowner 'p1' --satsbyte 30 

```
- `atom`指铸造的代币名称，根据实际情况修改
- `--satsbyte 30` 支付的gas
- `funding f1` 付款钱包，f1为wallet.json文件导入的钱包，别名f1
- `initialowner p1` 接收铭文钱包

#### 铸造Realm命令
```
yarn cli mint-realm btc --satsbyte 30 --satsoutput 1000 --bitworkc 3165
```

#### 铸造图片NFT命令
```
# 查看nft集合信息
yarn cli get-container "toothy"

# 查重（示例：查询编号9750的toothy有没有被别人打，返回为空代表没被打）
yarn cli get-container-item "#toothy" "9750"

# 铸造（示例：铸造编号为9750的toothy。容器名称'#toothy'，json文件存放位置'../../dmint/toothy/item-9740.json'，根据自己存放位置进行相应更改，gas 150）
yarn cli mint-item "#toothy" "9750" ../../dmint/toothy/item-9750.json --satsbyte 150
yarn cli mint-item "#toothy" "9750" ../../dmint/toothy/item-9750.json --funding 'f1' --initialowner 'p1' --satsbyte 150
```

铸造命令给出后会如果代币还有的话会给出收款二维码，地址以及需要的金额，充钱进去就会开始挖矿，挖矿成功会提示success。提前充钱进去会省很多时间。

#### 查询余额命令
```
# 查询atom余额
yarn cli balances atom
```

#### 查总量
```
# 查询atom总量
yarn cli location atom
```

#### 转移代币
```
# 转移atom
yarn cli transfer-ft atom
```
参考：https://mirror.xyz/xiaonezhalab.eth/BWpbBheadJFkjaTE4kzrwX5Yp_kLw5TAwagrfD2qFp4

### 2.2.4 拉取最新代码并重新编译

项目代码更新的话就需要拉取最新代码，并重新编译

```
# 拉取最新代码
git pull

# 安装依赖（有可能新代码使用了新的依赖）
yarn install

# 重新编译
yarn build
```

## 2.3 使用atomicalsir（rust版cli）

### 2.3.1 安装atomicals-js
步骤同上

### 2.3.2 安装atomicalsir

在atomicals-js同目录下执行以下命令
```
git clone https://github.com/hack-ink/atomicalsir.git
cd atomicalsir
cargo build --release
```

```
# 使用以下命令创建一个符号链接指向您的 atomicalsir 可执行文件
sudo ln -s ~/projects/blockchain/bitcoin/atomicals-project/rust/atomicalsir/target/release/atomicalsir /usr/local/bin/atomicalsir

sudo ./atomicalsir/target/release/atomicalsir --network testnet --rust-engine ./atomicalsir/target/release/atomicalsir --ticker atomicalsir4
sudo ./atomicalsir/target/release/atomicalsir --rust-engine ./atomicals-js/wallets --ticker atomicalsir4
```

## 2.4 节点

当mint的项目比较热门的时候可能会导致公共节点瘫痪，这时候就需要换成其他的节点试试。目前有几个公共节点可以使用。
```
# 官方节点
https://ep.atomicals.xyz/proxy
# NextDao节点
https://ep.nextdao.xyz/proxy
# Neutron节点
https://ep.atomicalneutron.com/proxy
# Consync节点
https://ep.consync.xyz/proxy
# Wizz.Cash节点
https://ep.atomicalswallet.com/proxy
# Atomical Market节点
https://ep.atomicalmarket.com/proxy
```
编辑.env文件，在`ELECTRUMX_PROXY_BASE_URL`后面添加节点即可，用逗号分隔。


# 三、通过客户端cli + 私有RPC节点 铸造代币

客户端cli部分同上。这部分重点介绍搭建私有Atomicals RPC节点。

项目依赖
- 一台cpu好点的电脑，一个2T的SSD固态硬盘（挖矿靠cpu，存储读写数据靠ssd固态硬盘。目前比特币全节点数据608G，electrumx数据94G，2T预留未来空间）
- docker
- btc全节点（bitcoin core）
- atomicals-electrumx
- electrumx-proxy

![image](https://github.com/gaohongxiang/atomicals/assets/33713367/92bbf577-6a06-4722-9ecd-1229eea598bd)

## 3.1 准备工作
由于比特币全节点数据和electrumx数据都非常大，从头开始同步的话会很慢，所以，我们可以提前下载别人打包好的数据，然后只需要同步后面新产生的数据就好了。这样会节省很多时间。

打包好的比特币节点数据（截止到2022.8）
- https://pan.quark.cn/s/4c3b98e42c1f#/list/share

在开通夸克网盘会员的情况下，大概9小时下载完毕。


打包好的electrumx数据磁力链接（推荐使用qBittorrent工具下载）
```
magnet:?xt=urn:btih:7KW5OXSWUQ2EFF57URE42GBRL2XCN5AI&dn=ElectrumX-Data-20231114&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=https%3A%2F%2Ftracker2.ctix.cn%3A443%2Fannounce&tr=https%3A%2F%2Ftracker1.520.jp%3A443%2Fannounce&tr=udp%3A%2F%2Fopen.tracker.cl%3A1337%2Fannounce&tr=udp%3A%2F%2Fopentracker.i2p.rocks%3A6969%2Fannounce&tr=udp%3A%2F%2Fopen.demonii.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.openbittorrent.com%3A80%2Fannounce&tr=udp%3A%2F%2Fopen.stealth.si%3A80%2Fannounce&tr=udp%3A%2F%2Ftracker.torrent.eu.org%3A451%2Fannounce&tr=udp%3A%2F%2Fexodus.desync.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker1.bt.moack.co.kr%3A80%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.moeking.me%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker-udp.gbitt.info%3A80%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=https%3A%2F%2Ftracker.gbitt.info%3A443%2Fannounce&tr=http%3A%2F%2Ftracker.gbitt.info%3A80%2Fannounce&tr=udp%3A%2F%2Fuploads.gamecoast.net%3A6969%2Fannounce
```

两个数据全部下载完成后，用`7-zip`解压，数据备用。

>解压后比特币数据包含`blocks`、`chainstate`两个文件夹。electruumx-data包含三个文件夹三个文件。


## 3.2 同步比特币节点

### 3.2.1 下载bitcoin core并配置

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

### 3.2.2 移动打包好的数据

将第一步下载的打包好的比特币数据移动到`bitcoin_data`文件夹下，替换掉`blocks`和`chainstate`这两个文件夹。

![image](https://github.com/gaohongxiang/atomicals/assets/33713367/cec0eae5-f0f8-42eb-b19c-cbe95d68f973)

### 3.2.3 同步比特币数据

再次打开bitcoin core软件，数据开始同步，可以看到，我们下载好的数据不需要重新同步了，只需要同步剩下的数据就好了，耐心等待，需要几个小时才能同步完。同步完成后就有了比特币全节点数据！

## 3.3 同步 ATOM 的索引数据

### 3.3.1 安装docker

官网：https://www.docker.com/

注册并登录

### 3.3.2 拉取atomicals-electrumx-proxy-docker并配置

##### 拉取atomicals-electrumx-proxy-docker
```
git clone https://github.com/Next-DAO/atomicals-electrumx-proxy-docker.git
```

##### 修改docker-compose.yml文件

docker-compose.yml文件内容如下

修改 `DAEMON_URL=username:password@ip:8332` 字段，换成自己的用户名密码和ip。用户名密码跟bitcoin配置文件一致。ip换成你比特币全节点所在的ip，局域网ip，如`192.168.0.1`。终端运行`ipconfig`命令可以查看

```
    environment:
      - DAEMON_URL=username:password@ip:8332
      - COIN=BitCoin
      - PEER_DISCOVERY=off
      - PEER_ANNOUNCE=""
      - MAX_SEND=3000000
```
---

方式2: 在atomicals-electrumx-proxy-docker目录下创建`.env`文件

内容如下
```
DAEMON_URL=username:password@ip:8332
```

然后将docker-compose.yml的DAEMON_URL字段换成如下
```
DAEMON_URL=${DAEMON_URL}
```

即：docker-compose.yml引用的.env文件的DAEMON_URL变量

两种方式选其一

---

### 3.3.3 移动electrumx数据

将下载好的electrumx-data文件夹移动到tomicals-electrumx-docker目录下

上面3步操作完成后重启电脑

### 3.3.4  拉取docker镜像并运行

首先确保终端在项目根目录下，不在就cd进去

##### 拉取镜像
```
docker-compose pull
```
##### 启动镜像
```
docker-compose up -d
```

启动后可以看到docker客户端开始同步区块数据。终端可以关闭。等待docker同步完数据。


##### 打印日志查看区块同步进度
```
docker-compose logs -f
```

只有等区块完全同步了才能开始用

## 3.4 替换客户端cli的RPC节点

### 3.4.1 检查本地节点状态：在浏览器地址栏输入 
```
http://127.0.0.1:8080/proxy/health
```
如果返回 “success”：true 则表示节点运行正常，等待区块数据同步后即可正常使用。

### 3.4.2 替换PRC节点
打开atomicals-js文件夹里的.env文件，替换原有节点URL为：
```
ELECTRUMX_PROXY_BASE_URL=http://localhost:8080/proxy
```

至此私有节点搭建完毕。


不用docker的方案，作为参考：https://twitter.com/zhangch30063843/status/1725851723930227095

---

有新项目时只需要提前同步好比特币节点数据和electrumx数据，并保持打开状态

然后执行客户端cli的相应mint命令即可用自己搭建的节点来mint代币了

---

### 3.4.3 常用命令

检查electrumx是否准备就绪
```
docker-compose ps
```
使用cmd查看进度
```
docker-compose logs -f
```
关闭服务器
```
docker-compose down
```

### 3.4.4 更新
项目代码更新的话就需要拉取最新代码，并重新编译

 `git pull`拉取最新代码，然后docker客户端删除之前镜像，重新执行3.3.4小节命令


### 3.4.5 常见错误

1、docker-compose不能用

这个错误是因为windows系统下的问题，好像没有wsl2，自己百度。

1、连接错误
```
ERROR:Daemon:connection problem - check your daemon is running.  Retrying occasionally...
```

这个错误是比特币配置文件没有配置好ip或者docker-compose.yml文件ip设置错误


## 参考
- Atomicals-JS部署及私有节点搭建：https://mirror.xyz/cyberscavenger.eth/rayhn9RT1VMUMo4Iios3g0A6G6AtZ-EXfQ8F_kj8HhQ
- Atomicals ElectrumX 节点 Server 简易安装教程:https://twitter.com/wusimpl/status/1724769629296570724
- nextDao方案：https://github.com/Next-DAO/atomicals-electrumx-docker
- Atomicals RPC 本地部署简明教程：https://twitter.com/millsonzhou/status/1724366513912807472
- Atomicals 前景及应用研讨会 RPC 节点相关链接：https://twitter.com/millsonzhou/status/1725498209014059466
- 如何搭建 Bitcoin Core 比特币全节点：https://yishi.io/how-to-run-bitcoin-full-node/
- 如何极速同步Bitcoin 全节点：https://twitter.com/weixiaoqvq/status/1716014155730465267
- Atomicals私有节点数据下载磁力链接：https://mirror.xyz/cyberscavenger.eth/bjOEWmZt_LaQsvDj2o8_uO_X0SXdaT4S51Tfr0gva0g
- Atomicals dmint 进阶指南：https://github.com/wusimpl/atommical-js-tools/blob/main/Atomicals%20dmint%20%E8%BF%9B%E9%98%B6%E6%8C%87%E5%8D%97.pdf
