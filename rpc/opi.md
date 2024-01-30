开放协议索引器 (OPI) 是比特币元协议中最好的开源索引客户端。

项目github：https://github.com/bestinslot-xyz/OPI

OPI全节点搭建教程:https://cyberscavenger.notion.site/OPI-debfbead088648279a5c42cba8391a6c

教程是windows和ubuntu系统的。mac系统

其他的安装看教程，这里说下mac安装postgresql数据库。

## 安装postgresql数据库

使用brew安装
```
brew install postgresql
```

查询brew管理的程序
```
brew services list
```

启动
```
brew services start postgresql@14

pg_ctl -D /opt/homebrew/var/postgresql@14 -l logfile start

```

停止
```
brew services stop postgresql@14

pg_ctl -D /opt/homebrew/var/postgresql@14 stop
```

重启
```
brew services restart postgresql@14
```

查看日志文件
```
cat /opt/homebrew/var/log/postgresql@14.log
```
有时候postgresql报错需要看下日志文件定位错误


如果没有postgres用户需要创建
```
createuser -s -r postgres
```
创建密码
```
psql -U postgres -d postgres # 进入数据库
ALTER USER postgres PASSWORD '新密码'; # 设置密码
\q # 推出数据库
```

查看数据库的所有表格
```
psql -U postgres -d postgres # 进入数据库
\dt # 查询数据表列表

SELECT * FROM table_name; # 查询数据表数据

空格翻页
g跳到开始
G跳到结尾
\q退出数据表
```


>注意：数据库设置好了需要开启状态

然后通过此教程安装OPI官方程序：https://cyberscavenger.notion.site/OPI-a32f98836879497898ede636b380a1c7

然后运行restore.py文件下载备份数据，减少同步时间

```
cd modules
python3 restore.py
```


最后运行各节点

```
cd main_index
node index.js
```

```
cd brc20_index
python brc20_index.py
```

```
cd brc20_abi
node api.js
```
