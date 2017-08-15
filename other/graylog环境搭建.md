## Graylog安装

### 1.安装MongoDB

`vim /etc/yum.repos.d/mongodb-org-3.0.repo`，添加如下内容：

```shell
[mongodb-org-3.0]
name=MongoDB Repository
baseurl=http://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.0/x86_64/
gpgcheck=0
enabled=1
```

执行下面的命令：

```shell
$ yum install -y mongodb-org
$ sudo chkconfig --add mongod
$ sudo systemctl daemon-reload
$ sudo systemctl enable mongod.service
$ sudo systemctl start mongod.service
```

### 2.安装elasticsearch

`vim /etc/yum.repos.d/elasticsearch.repo `，添加如下内容：

```shell
[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=https://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=https://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
```

然后执行下面哦命令进行安装：

```shell
$ yum install elasticsearch.
$ sudo chkconfig --add elasticsearch
$ sudo systemctl daemon-reload
$ sudo systemctl enable elasticsearch.service
$ sudo systemctl restart elasticsearch.service
```

修改配置文件``/etc/elasticsearch/elasticsearch.yml`)`:

```shell
cluster.name: graylog
```

### 3.安装Graylog

执行以下命令进行安装：

```shell
$ rpm -Uvh https://packages.graylog2.org/repo/packages/graylog-2.2-repository_latest.rpm
$ yum install graylog-server
$ chkconfig --add graylog-server
$ systemctl daemon-reload
$ systemctl enable graylog-server.service
$ systemctl start graylog-server.service
```

编辑``/etc/graylog/server/server.conf` ，按提示设置`password_secret`和`root_password_sha2`



