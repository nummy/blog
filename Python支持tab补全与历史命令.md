Python 命令行默认是不支持tab补全与上下查看历史命令的，而IPython是支持这一功能的，那如何让Python支持上述两个功能呢？

可以通过设置python启动时执行指定脚本来添加这两个功能。通过配置环境变量PYHTONSTARTUP可以指定启动时执行的脚本。

在当前用户目录下创建`.pythonstartup`文件，文件内容如下：

```python
# python startup file
import readline
import rlcompleter
import atexit
import os

# tab completion
readline.parse_and_bind('tab: complete')

# history file
histfile = os.path.join(os.environ['HOME'], '.pythonhistory')

try:
    readline.read_history_file(histfile)
except IOError:
    pass

atexit.register(readline.write_history_file, histfile)

del os, histfile, readline, rlcompleter                                     
```

然后在`~/.bashrc`中添加环境变量：

```bash
export PYTHONSTARTUP=~/.pythonstartup
```

然后重新读取环境变量

```bash
source ~/.bashrc
```

大功告成，读者可以自己试下是否支持tab补全，与上下翻看历史命令了。