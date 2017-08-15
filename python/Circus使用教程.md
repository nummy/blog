Circus是与supervisor类似的进程监控工具，它也是使用Python编写的。

下面我们使用circus来监控一个`wsgi`应用。

### 安装

Circus支持python2.6, 2.7,3.2 以及3.3，安装Circus之前，需要先安装一些开发包：

```bash
$ yum install libzmq-dev libevent-dev python-dev python-virtualenv
```

然后创建虚拟环境，并安装circus

```shell
$ virtualenv /tmp/circus
$ cd /tmp/circus
$ bin/pip install circus
$ bin/pip install circus-web
$ bin/pip install chaussette
```

执行完上述命令之后，在虚拟环境的bin目录会增加很多可执行脚本。



### 使用

*Chaussette* 提供了一个简单的`hello world`应用，执行以下命令：

```shell
$ bin/chaussette
```

访问页面`http://localhost:8080`，页面将返回`hello world`。

停止`Chaussette`，在当前目录添加`circus.ini`，内容如下：

```ini
[circus]
statsd = 1
httpd = 1

[watcher:webapp]
cmd = bin/chaussette --fd $(circus.sockets.web)
numprocesses = 3
use_sockets = True

[socket:web]
host = 127.0.0.1
port = 9999
```

上面的配置文件告诉Circus，绑定socket到9999端口，并启动3个chaussettes进程，同时启动Circus web控制面板，并开启统计模块。

保存文件之后，执行`circusd`

```shell
$ bin/circusd --daemon circus.ini
```

现在访问`http://127.0.0.1:9999`，就可以访问到hello world应用，跟前面例子的区别是现在socket改由Circus来管理。

还可以通过`http://localhost:8080`访问控制面板。

###　交互

使用`circusctl`来管理监控的进程。

```shell
$ bin/circusctl
circusctl 0.7.1
circusd-stats: active
circushttpd: active
webapp: active
(circusctl)
```

进入交互控制台之后，输入`help`获取所有的命令：

```shell
(circusctl) help

Documented commands (type help <topic>):
========================================
add     get            list         numprocesses  quit     rm      start   stop
decr    globaloptions  listen       numwatchers   reload   set     stats
dstats  incr           listsockets  options       restart  signal  status

Undocumented commands:
======================
EOF  help
```

列出所有的web进程，并增加一个：

```shell
(circusctl) list webapp
13712,13713,13714
(circusctl) incr webapp
4
(circusctl) list webapp
13712,13713,13714,13973
```

此外，还可以使用circus-top查看当前进程占用内存、CPU的实时情况：

```shell
$ bin/circus-top
```

使用下面命令完全停止Circus

```shell
$ bin/circusctl quit
ok
```

