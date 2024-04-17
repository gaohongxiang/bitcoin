前提：开启比特币全节点

# 同步ord索引

## 下载最新ord版本

https://github.com/ordinals/ord/releases

根据自己电脑系统下载，mac m系列芯片下载`ord-xxx-aarch64-apple-darwin.tar.gz`版本，解压，放在你想放的位置，比如我放在`~/bitcoin/ordinals`下面，假设下载的是0.18.1版本，那么项目目录就是`~/bitcoin/ordinals/ord-0.18.1`

终端进入项目目录，后面操作都是在项目目录下
```
cd ~/bitcoin/ordinals/ord-0.18.1
```

## 配置config文件

项目目录下创建config文件，配置如下，并附有详细说明

```
# bitcoin core相关配置，通过用户名、密码连接，指定bitcoin_data存放位置
bitcoin_data_dir: <替换成自己的bitcoin存放数据的目录>
bitcoin_rpc_username: <替换成自己的bitcoin_rpc用户名>
bitcoin_rpc_password: <替换成自己的bitcoin_rpc密码>

no_index_inscriptions: true #不索引铭文
index_runes: true #索引符文
data_dir: ./ #索引数据存放位置，./为项目当前目录下。默认会存储在～/Library/Application Support/ord目录
# index_cache_size: 26000000000 #索引用的内存大小，默认使用1/4本机内存
```

#### 查看配置
```
./ord --config config settings
```

## 更新索引

```
./ord --config config index update
```

如果只索引符文的话，大概几个小时就能索引完成

## 启动服务

```
./ord --config config server
```

本地浏览器打开 `http://0.0.0.0/runes` 即可看到目前所有的runes列表


>启动服务后这个命令行终端需要保持开启，后续命令需要重新打开一个命令行终端来运行


# 常用命令

钱包
```
//查看钱包所有命令
./ord wallet

// 创建钱包
./ord --config config wallet create

//接受地址
./ord --config config wallet receive

//查询余额
./ord --config config wallet balance
```


列出所有的runes代币
```
./ord --config config runes
```

# 参考

- 官方github；https://github.com/ordinals/ord
- 官方文档：https://docs.ordinals.com/zh/introduction.html
- macos版runes全节点新教程（推特用户ohxiyu）：https://twitter.com/ohxiyu/status/1778365892130373729
- windows版runes全节点新教程（推特用户practice_y11）：https://twitter.com/practice_y11/status/1779734448415895772
- 0.18.0索引数据：https://twitter.com/practice_y11/status/1778959363992121659
