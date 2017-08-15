### 安装

下载最新的maven包：

```shell
wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz
```

解压：

```shell
tar xzvf apache-maven-3.5.0-bin.tar.gz
mv apache-maven-3.5.0-bin.tar.gz /usr/local/maven
```

配置环境变量, 编辑`~/.bash_profile`：

```shell
PATH=/usr/local/maven/bin:$PATH:$HOME/bin
export PATH
```

然后执行

```
source ~/.bash_profile
```

输入即可看到版本信息

```
mvn -v
```

### 配置国内源镜像

编辑`/usr/local/maven/conf/settings.xml`

```xml
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```

### 创建项目

执行以下命令，创建项目:

```shell
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

然后在当前目录下会生成一个文件夹`my-app`。

### 常用命令

```shell
# 打包
mvn package
# 查看帮助
mvn -h
# 打包并将jar包存放到本地依赖池
mvn clean install
```

