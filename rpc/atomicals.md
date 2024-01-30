## 准备工作

由于electrumx数据非常大，从头开始同步的话会很慢，所以，我们可以提前下载别人打包好的数据，然后只需要同步后面新产生的数据就好了。这样会节省很多时间。

打包好的electrumx数据磁力链接（推荐使用qBittorrent工具下载）
```
magnet:?xt=urn:btih:7KW5OXSWUQ2EFF57URE42GBRL2XCN5AI&dn=ElectrumX-Data-20231114&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=https%3A%2F%2Ftracker2.ctix.cn%3A443%2Fannounce&tr=https%3A%2F%2Ftracker1.520.jp%3A443%2Fannounce&tr=udp%3A%2F%2Fopen.tracker.cl%3A1337%2Fannounce&tr=udp%3A%2F%2Fopentracker.i2p.rocks%3A6969%2Fannounce&tr=udp%3A%2F%2Fopen.demonii.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.openbittorrent.com%3A80%2Fannounce&tr=udp%3A%2F%2Fopen.stealth.si%3A80%2Fannounce&tr=udp%3A%2F%2Ftracker.torrent.eu.org%3A451%2Fannounce&tr=udp%3A%2F%2Fexodus.desync.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker1.bt.moack.co.kr%3A80%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.moeking.me%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker-udp.gbitt.info%3A80%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=https%3A%2F%2Ftracker.gbitt.info%3A443%2Fannounce&tr=http%3A%2F%2Ftracker.gbitt.info%3A80%2Fannounce&tr=udp%3A%2F%2Fuploads.gamecoast.net%3A6969%2Fannounce
```

数据下载完成后，用`7-zip`解压，数据备用。

>解压后electruumx-data包含三个文件夹三个文件。

## 同步 ATOM 的索引数据

### 1、安装docker

官网：https://www.docker.com/

注册并登录

### 2、拉取atomicals-electrumx-proxy-docker并配置

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

### 3、移动electrumx数据

将下载好的electrumx-data文件夹移动到tomicals-electrumx-docker目录下

上面3步操作完成后重启电脑

### 4、拉取docker镜像并运行

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

## 替换客户端cli的RPC节点

### 1、检查本地节点状态：在浏览器地址栏输入 
```
http://127.0.0.1:8080/proxy/health
```
如果返回 “success”：true 则表示节点运行正常，等待区块数据同步后即可正常使用。

### 2、替换PRC节点
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

### 3、常用命令

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

### 4、更新
项目代码更新的话就需要拉取最新代码，并重新编译

 `git pull`拉取最新代码，然后docker客户端删除之前镜像，重新执行3.3.4小节命令


### 5、常见错误

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
- Atomicals私有节点数据下载磁力链接：https://mirror.xyz/cyberscavenger.eth/bjOEWmZt_LaQsvDj2o8_uO_X0SXdaT4S51Tfr0gva0g
- Atomicals dmint 进阶指南：https://github.com/wusimpl/atommical-js-tools/blob/main/Atomicals%20dmint%20%E8%BF%9B%E9%98%B6%E6%8C%87%E5%8D%97.pdf
