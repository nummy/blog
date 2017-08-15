## 安装logstash

首先必须先安装Java，并配置JAVA_HOME环境变量，然后从官网下载logstash的tar包。

```shell
$ tar -zxvf logstash-5.4.0.tar.gz
$ mv logstash-5.4.0 /usr/local/
```

## 收集日志

Logstash管道包含了输入和输出两部分，还有一个可选的过滤器。input插件从输入源获取数据，filter插件用于修改数据，output插件将数据写入到目的地。

![static/images/basic_logstash_pipeline.png](https://www.elastic.co/guide/en/logstash/current/static/images/basic_logstash_pipeline.png)

通过下面的命令运行一条最简单的管道：

```shell
$ cd logstash-5.5.0
$　bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

`-e`可以直接从命令行中指定配置参数，这样方便我们快速的进行调试。

启动Logstash之后，当看到`Pipline main started`之后，输入`hello world`，然后就会看到下面的输出：

```
hello world
2013-11-21T01:22:14.405+0000 0.0.0.0 hello world
```

Logstash给输出消息添加了时间戳和IP字段。

## 使用Logstash解析日志

在前面的例子中，我们只是创建了一个简单的管道，但是在实际应用中，远比这种情况更为复杂，可能涉及到多种输入，过滤和输出插件。

下面我们使用Logstash来解析apache日志。首先需要安装filebeat，使用filebeat发送日志给logstash。

安装完filebeat之后，修改配置文件`filebeat.yml`如下：

```yaml
filebeat.prospectors:
- input_type: log
  paths:
    - /path/to/file/logstash-tutorial.log 
output.logstash:
  hosts: ["localhost:5043"]
```

然后启动filebeat

```shell
$ sudo ./filebeat -e -c filebeat.yml -d "publish"
```

接下来配置logstash，创建配置文件`pipeline.conf`:

```ini
input {
    beats {
        port => "5043"
    }
}
# The filter part of this file is commented out to indicate that it is
# optional.
# filter {
#
# }
output {
    stdout { codec => rubydebug }
}
```

检查配置文件是否正确：

```shell
$　bin/logstash -f config/pipeline.conf --config.test_and_exit
```

启动logstash

```shell
$　bin/logstash -f config/pipeline.conf --config.reload.automatic
```

