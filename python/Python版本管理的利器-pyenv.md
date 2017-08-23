pyenv用于在同一个系统管理多个Python版本。

## 安装
使用如下命令进行安装：
```
$ curl -L https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash
```
编辑`~/.bash_profile`:
```
export PATH="~/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```
使用如下命令更新：
```
$ pyenv update
```
## 常用命令

跟git类似，pyenv也通过第一个参数分发子命令。

最常用的命令如下：

- `pyenv commands`
- `pyenv local`
- `pyenv global`
- `pyenv shell`
- `pyenv install`
- `pyenv uninstall`
- `pyenv rehash`
- `pyenv version`
- `pyenv versions`
- `pyenv which`

**pyenv commands**

列出所有命令

**pyenv local**

设置局部python版本，将Python版本写进当前目录下的`.python-version` 文件，这个版本号会覆盖全局的版本号，可以被`PYENV_VERSION`或者`pyenv shell`命令覆盖。

```
$ pyenv local 2.7.6
```

当不指定版本号时，则输出当前局部版本号，也可以通过`unset`取消设置:

```
$ pyenv local --unset
```

**pyenv global**

设置全局Python版本，版本号会写入`~/.pyenv/version`，它可以被局部版本号(通过.python-version）或者环境变量`PYENV_VERSION`覆盖。

```shell
$ pyenv global 2.7.6
```

`system`表示系统默认的Python版本。

**pyenv shelll**

设置shell启动时使用的Python版本，也可以通过环境变量 `PYENV_VERSION`进行设置。它会覆盖局部版本号和全局版本设置。

```
$ pyenv shell 3.6.1
```

取消设置

```
$ pyenv shell --unset
```

**pyenv install**

安装指定Python版本，语法如下：

```
Usage: pyenv install [-f] [-kvp] <version>
       pyenv install [-f] [-kvp] <definition-file>
       pyenv install -l|--list

  -l/--list             List all available versions
  -f/--force            Install even if the version appears to be installed already
  -s/--skip-existing    Skip the installation if the version appears to be installed already

  python-build options:

  -k/--keep        Keep source tree in $PYENV_BUILD_ROOT after installation
                   (defaults to $PYENV_ROOT/sources)
  -v/--verbose     Verbose mode: print compilation status to stdout
  -p/--patch       Apply a patch from stdin before building
  -g/--debug       Build a debug version
```

可以先使用如下命令列出所有可安装的Python版本：

```
$ pyenv install --list
```

安装指定版本的python

```
$ pyenv install 2.7.6
$ pyenv install 2.6.8
$ pyenv versions
  system
  2.6.8
* 2.7.6 (set by /home/yyuu/.pyenv/version)
```

**pyenv uninstall**

卸载指定Python版本：

```
Usage: pyenv uninstall [-f|--force] <version>

   -f  Attempt to remove the specified version without prompting
       for confirmation. If the version does not exist, do not
       display an error message.
```

**pyenv version**

展示当前激活的Python版本以及相关设置信息：

```
$ pyenv version
2.7.6 (set by /home/yyuu/.pyenv/version)
```

**pyenv versions**

列出pyenv管理的Python版本，当前激活的版本前面带*

```
$ pyenv versions
  2.5.6
  2.6.8
* 2.7.6 (set by /home/yyuu/.pyenv/version)
  3.3.3
  jython-2.5.3
  pypy-2.2.1
```

**pyenv which**

显示指定版本的详细执行路径

```
$ pyenv which python3.3
/home/yyuu/.pyenv/versions/3.3.3/bin/python3.3
```

