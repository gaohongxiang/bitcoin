铸造代币有两种参与方式
1. 通过代打平台铸造代币
2. 通过客户端cli铸造代币

通过带打平台操作简单，按照步骤一步步做就可以了。但有可能大流量导致网页无法正常工作，另外代打平台有可能会收费。

常见的代打平台

- wizz官网:https://wizz.cash/mining (推荐，更新比较快。mint速度非常快。钱包的mint入口进入)
- satsx官网：https://www.satsx.io/atomicals
- atom市场：https://atomicalmarket.com/inscribe 

通过cli铸造代币是命令行方式，直接跟协议交互，比较适合有编程基础的朋友。下面是具体操作流程

# 通过客户端cli铸造代币

目前有两个客户端，一个是atomicals协议官方js版本的`atomicals-js`，另一个是推特用户AurevoirXavier写的rust版本的`atomicalsir`。两个用哪个都行，rust版本不是纯净版，目前依赖atomicals-js，引擎可以选rust，速度会比js快。

>注：命令都是在终端中执行的，win下的终端如`cmd`、`PowerShell`都需要右键以管理员身份打开，否则容易权限不足

## 准备

### 1、安装git

https://git-scm.com/downloads

终端输入命令查看node版本
```
git -v
```

### 2、安装node

https://nodejs.org/en

终端输入命令查看node版本
```
node -v
```

### 3、安装rust
```
# 安装rustup（rust的包管理工具）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# 安装nightly版rust（atomicalsir使用的此版本）
rustup install nightly
# 查看版本
rustc --version
```

## 使用atomicals-js（js版cli）

### 1、拉取并编译项目
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

### 2、创建钱包
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

### 3、常用命令

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

#### 帮助命令
```
# 总的帮助命令
yarn cli -h
# dft的帮助命令
yarn cli mint-dft -h
# item的帮助命令
yarn cli mint-item -h
```

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

### 4、拉取最新代码并重新编译

项目代码更新的话就需要拉取最新代码，并重新编译

```
# 拉取最新代码
git pull

# 安装依赖（有可能新代码使用了新的依赖）
yarn install

# 重新编译
yarn build
```

## 使用atomicalsir（rust版cli）

### 1、安装atomicals-js
步骤同上

### 2、安装atomicalsir

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

## 节点

### 公共节点
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

### 私有节点

搭建参考rpc/atomicals.md章节

#### 1、检查本地节点状态：在浏览器地址栏输入 
```
http://127.0.0.1:8080/proxy/health
```
如果返回 “success”：true 则表示节点运行正常，等待区块数据同步后即可正常使用。

#### 2、替换PRC节点
打开atomicals-js文件夹里的.env文件，替换原有节点URL为：
```
ELECTRUMX_PROXY_BASE_URL=http://localhost:8080/proxy
```
