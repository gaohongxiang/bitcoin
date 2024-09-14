2024/9/11日，Fractal Bitcoin上的CAT协议铸造教程

### 环境配置

默认mac环境下

- docker
- docker-compose
- node.js

自行搜索

### 1、编译项目
```
git clone https://github.com/CATProtocol/cat-token-box
cd cat-token-box
sudo yarn install
sudo yarn build
```

### 2、运行 docker 容器

CAT 是在 Fractal 主网上的协议，所以需要在服务器上运行一个 Fractal 全节点，在docker中运行，由于现在 Fractal 网络的区块不多，所以同步是特别快的.

运行 Fractal 节点：
```
cd ./packages/tracker/
sudo chmod 777 docker/data
sudo chmod 777 docker/pgdata
sudo docker-compose up -d
```

同时，CAT Protocol 需要运行一个本地的索引器，下面是编译和运行的命令
```
cd ../../
sudo docker build -t tracker:latest .
sudo docker run -d \
    --name tracker \
    --add-host="host.docker.internal:host-gateway" \
    -e DATABASE_HOST="host.docker.internal" \
    -e RPC_HOST="host.docker.internal" \
    -p 3000:3000 \
    tracker:latest
```

### 3、创建钱包

```
sudo yarn cli wallet create
```

### 4、查看钱包地址
```
sudo yarn cli wallet address
```

转fb进钱包地址，然后可以将助记词导入unisat，选择正确的钱包地址（没转入资金前，unisat默认不显示这个地址）

### 5、铸造

#### 单次铸造

```
sudo yarn cli mint -i 45ee725c2c5993b3e4d308842d87e973bf1951f5f7a804b21e4dd964ecd12d6b_0 5 --fee-rate 500
```

>fee-rate调节gas费，根据当前gas情况来定


#### 批量铸造

在cli下创建脚本 `script.sh`，写入以下内容

```
#!/bin/bash

# 默认值
fee_rate=500

# 解析命令行参数
while [[ "$#" -gt 0 ]]; do
    case $1 in
        --fee-rate) fee_rate="$2"; shift ;;
        *) echo "未知参数: $1"; exit 1 ;;
    esac
    shift
done

command="sudo yarn cli mint -i 45ee725c2c5993b3e4d308842d87e973bf1951f5f7a804b21e4dd964ecd12d6b_0 5 --fee-rate $fee_rate"

while true; do
    $command

    if [ $? -ne 0 ]; then
        echo "命令执行失败，退出循环"
        exit 1
    fi

    sleep 1
done

```

添加运行权限

```
chmod +x script.sh
```

执行循环操作

```
// 使用默认gas 500
./script.sh

// 使用指定gas 1000
./script.sh --fee-rate 1000
```

#### 关闭merge

由于项目热度高，打的人多，导致gas居高不下。而官方代码每次mint前都会先merge一下，这个操作特别贵，mint的时候可以先关了这个操作，等mint结束后gas降下来再进行merge操作，会节省很多gas。

1、项目目录下搜索`this.merge`,定位文件`mint.command.ts`，将`this.merge`这一行代码注释掉。
2、删除`cli`目录下的`dist`文件夹 : `sudo rm -rf dist`
3、重新执行`sudo yarn build`

当mint完成后，打开merge操作，上面步骤反向操作。然后执行mint操作，程序会先merge代币，merge流程结束就可以停止程序了。

### 常用命令

```
// 查区块同步：
sudo docker logs tracker -f | grep "block"

// 创建钱包
sudo yarn cli wallet create

// 查钱包地址
sudo yarn cli wallet address

// 查询mint了多少cat
sudo yarn cli wallet balances

// mint 命令（带gas）
sudo yarn cli mint -i 45ee725c2c5993b3e4d308842d87e973bf1951f5f7a804b21e4dd964ecd12d6b_0 5 --fee-rate 500

// 转账
sudo yarn cli send -i 45ee725c2c5993b3e4d308842d87e973bf1951f5f7a804b21e4dd964ecd12d6b_0 接收地址 数量 --fee-rate 500
```

### 常见错误

#### 1、Insufficient satoshis balance!

如果明明钱包里有钱却报`Insufficient satoshis balance!`错误，有两种可能，第一是节点没有同步，第二是创建的钱包没有正确导入节点。

第二点可以通过运行下面的指令来修复。

```
sudo yarn cli wallet export --create=true

```


FB 浏览器（查交易、查余额、查gas）

- https://mempool.fractalbitcoin.io/
- https://explorer.unisat.io/fractal-mainnet
- https://www.oklink.com/zh-hans/fractal-bitcoin


整体 mint 情况

https://explorer.unisat.io/fractal-mainnet/address/bc1plhz9wf0desgz8t32xm67vay9hgdmrnwzjzujgg0k9883cfxxgkzs20qfd5