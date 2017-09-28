## MongoDB分布式集群

MongDB分布式集群能够对数据进行备份，提高数据安全性，以及提高集群提高读写服务的能力和数据存储能力。主要通过副本集(replica)对数据进行备份，通过分片(sharding)对大的数据进行分割，分布式存储在不同节点上。

### 副本集(replica)

副本集由若干台服务器组成，分为三种角色：主服务器、副服务器、仲裁服务器。根据集群搭建的需求，仲裁服务器不是必需的。主服务器提供主要的对外读写的功能，副服务器作为备份。当主服务器不可用时，其余服务器根据投票选出一个新的主服务器，提供读写功能。因此，副本集可以提高集群的可用性。

### 分片(sharding)

分片主要是为减小高数据量和高吞吐量的数据库应用对单机性能造成的压力。将大的数据分片存储在不同节点上，外部读写只操作相应的一个或一小部分节点，一次减少每个分片节点村春的数据量和处理的请求数 。

## Mongo集群部署

> 注意：在生产环境中，配置服务器务必使用三个，而不是一个；每个分片节点都部署成副本集，而不是一个单独的Mongo服务器



### 配置文件

配置文件用于在启动mongod时加载配置，也可以使用该命令行启动项，不过配置项很多的时候，命令行参数很多。所以应该把配置项都写到配置文件中。每个节点都有一个配置文件。

配置文件主要包括以下配置项：

- dbpath = \ 指定数据的存放位置，必需项
- logpath = \ 指定日志的存放位置
- logappend = \ 日志以追加方式写入
- pidfilepath = \ 存放启动mongod是分配的进程号
- bind_ip = \ mongod监听的ip，可以不设置，不设置时，通过机器的ip访问
- port = \监听的端口号，务必设置，默认的端口不安全
- directoryperdb = \为每个数据库的数据分配一个存储目录，建议设置，数据更好管理
- journal=\ 启用恢复日志，如果mongod意外退出，下一次启动时会根据恢复日志进行恢复，但恢复日志所占空间比较大。建议设置true
- keyFile = \指定使用的key的路径，集群中的所有节点都要使用相同的key才能相互连接。（在集群搭建完成之前，不应当使用keyFile，否则在部署副本集和分片时会出现没有权限操作的情况）
- auth = \ 是否使用授权认证机制，集群使用时，应当使用auth=true，但在集群部署时不应该使用auth=true
- noprealloc = \ 是否预分配空间，预分配空间比较占空间；不预分配空间可能对性能有影响。
- replSet = \节点所属副本集的名称
- **fork 务必将fork选项设置为true，否则当启动节点的终端意外退出时，节点的运行进程会被杀掉**

### 副本集部署

以下以部署一个有三个节点（一个primary，一个secondary， 一个arbiter）[1](http://blog.csdn.net/jalused/article/details/46946521#fn:2)rs0的副本集为例，并假设三个节点的hostname是：hostname_primary:1111, hostname_secondary:2222, hostname_arbiter:3333。其中端口号按需求定；另外，假设三个节点配置文件的路径分别为：config_path_primary, config_path_secondary, config_path_arbiter。**此时，配置文件中不应该设置keyFile和auth两个配置项** 
replSet配置项应该设置为rs0，否则在下面的步骤中会遇到下面的错误

```
{
    "ok" : 0,
    "errmsg" : "Attempting to initiate a replica set with name rs0, but command line reports rs1; rejecting",
    "code" : 93
}
```

部署步骤：

1.启动三个节点

```
mongod -f config_path_primary 
mongod -f config_path_secondary
mongod -f config_path_arbiter
```

2.在primary节点所在的机器登陆上primary节点

```
mongo --port 1111
```

3.初始化

```
rs.initiate({_id : "rs0", members : [{_id : 0, host : "hostname_primary:1111"}]})
```

结果应该如下

```
{"ok" : 1}
```

4.添加secondary节点

```
rs.add("hostname_secondary:2222")
```

结果如下

```
{"ok" : 1}
```

5.添加仲裁节点

```
rs.addArb("hostname_arbiter:3333")
```

结果还是如下：

```
{"ok" : 1}
```

到了这里，一个三个节点的副本集rs0就部署好了^-^，如果需要部署更多节点，执行步骤4。

现在运行

```
rs.conf()
```

可以看到如下结果：

```
{
    "_id" : "rs1",
    "version" : 3,
    "members" : [
            {
                    "_id" : 0,
                    "host" : "localhost:4094",
                    "arbiterOnly" : false,
                    "buildIndexes" : true,
                    "hidden" : false,
                    "priority" : 1,
                    "tags" : {

                    },
                    "slaveDelay" : 0,
                    "votes" : 1
            },
            {
                    "_id" : 1,
                    "host" : "localhost:4095",
                    "arbiterOnly" : false,
                    "buildIndexes" : true,
                    "hidden" : false,
                    "priority" : 1,
                    "tags" : {

                    },
                    "slaveDelay" : 0,
                    "votes" : 1
            },
            {
                    "_id" : 2,
                    "host" : "localhost:4096",
                    "arbiterOnly" : true,
                    "buildIndexes" : true,
                    "hidden" : false,
                    "priority" : 1,
                    "tags" : {

                    },
                    "slaveDelay" : 0,
                    "votes" : 1
            }
    ],
    "settings" : {
            "chainingAllowed" : true,
            "heartbeatTimeoutSecs" : 10,
            "getLastErrorModes" : {

            },
            "getLastErrorDefaults" : {
                    "w" : 1,
                    "wtimeout" : 0
            }
    }
}
```

### 分片部署

分片部署就是要将几个不同的副本集联系起来。现在部署一个有三个配置服务器，一个mongos，一个分片的集群。

1.部署配置服务器，三个配置服务器的配置文件分别为:配置服务器也是mongod实例，所以需要在配置文件中指示其作为配置服务器运行,加上选项

```
configsvr=true
```

不应该有选项

```
replSet=rs0
```

因为它不是作为副本集的节点运行。

设三个配置服务器的hostname分别为: hostname_config_1, hostname_config_2, hostname_config_3，端口分别为：4444， 5555， 6666

启动三个配置服务器：

```
mongod -f config_path_conf1
mongod -f config_path_conf2
mongod -f config_path_conf3
```

2.部署mongos服务器，设其hostname为host_name_mongos，端口为8888。其配置文件路径为config_path_mongos，由于mongos不存储数据，所以不需要dbpath 选项。同时由于mongos要从配置服务器上获取集群的配置信息，所以需要制定配置服务器的hostname和端口，加上选项configdb

```
configdb = hostname_config_1:4444, hostname_config_2 : 5555, hostname_config_3 : 6666
```

启动mongos服务器

```
mongos -f config_path_mongos
```

注意这里是mongos，不是mongod。不是我打错字了！

3.在mongos所在机器登陆mongos服务器 
mongo –port 8888 
此时，运行

```
sh.status()
```

你会发现，shards一项里什么都没有，这是因为我们还没有给这个集群加分片。

4.添加rs0成为集群的分片

```
sh.addShard("rs0/hostname_primary:1111")
```

这里括号里面只需要是副本集名加上一个副本集中的成员即可，不一定要是primary节点。如：

```
sh.addShard("rs0/hostname_secondary:2222")
sh.addShard("rs0/hostname_arbiter:3333")
```

也是可以的。如果得到如下结果： 
{“shardAdded” : “rs1”, “ok” : 1} 
那么添加分片节点成功了。现在再运行

```
sh.status()
```

得到的结果为

```
sharding version: {
    "_id" : 1,
    "minCompatibleVersion" : 5,
    "currentVersion" : 6,
    "clusterId" : ObjectId("559f7fc9d8cec40f5a0f7609")
}
shards:
    {  "_id" : "rs0",  "host" : "rs0/hostname_primary:1111,hostname_secondary:2222" }
balancer:
    Currently enabled:  yes
    Currently running:  no
    Failed balancer rounds in last 5 attempts:  0
    Migration Results for the last 24 hours: 
            No recent migrations
databases:
    {  "_id" : "admin",  "partitioned" : false,  "primary" : "config" }
```

shards不为空了，rs成为了一个shard节点

### 权限认证设置

权限认证是非常重要的，生产环境中的集群必需有权限认证，而且需要比较严格的权限认证。

1.创建第一个用户

在上面部署成功的集群上执行以下步骤，在数据库admin中创建第一个具有最高root权限的用户root:

```
use admin
db.createUser({user : "root", pwd : "q,.wemr213oiz923*(*LNY", roles : [{role : "root", db : "admin"}]})
```

2.关闭所有上面部署的节点，可以用

```
db.shutdownServer()
```

也可以暴力kill

3.产生keyFile，并复制到每个运行集群节点的服务器上。

```
openssl rand -base64 741 > mongodb-keyfile
chmod 600 mongodb-keyfile
```

4.在每个节点的配置文件中加上选项：

```
keyFile = <key_file_path>
```

5.在出mongos外的所有节点的配置文件中加上选项

```
auth = true
```

6.重启所有节点，到此权限认证已经搞完了，现在就可以插入数据库，并按需求添加用户，赋予相应的权限。进行认证授权的函数为db.auth(), 例如：

```
db.auth("root", "<password>")
```

此时拥有root权限，可以进行一切操作。

------

1. 其实三个节点可以分为两种角色: 存储数据的节点（primary和secondary）， 不存储数据的节点（arbiter）， primary和secondary角色在存储数据的节点间是动态变化的。

