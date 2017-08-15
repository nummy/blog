Notebook支持numpy和matplotlab图像嵌入，只需在notebook起始位置运行下面的代码。

```python
%pylab inline
```

**1.魔法命令**

IPython中以`%`或者`%%`开头的命令称之为魔术命令，以`%`开头的称之为基于行的魔法命令，以`%%`开头的称之为基于单元格的魔法命令。

常用魔法命令有：

- `%timeit`测试某行代码的运行时间
- `%%timeit`测试整个单元格代码段的运行时间
- `%lsmagic`列出所有的魔法命令，获取命令帮助`%timeit?`
- `%autosave`设置notebook自动存档的间隔时间，默认为2分钟，单位为秒， 如果需要取消自动保存，运行`%autosave 0`即可， notebook以`.ipynb`的后缀存储，文件格式其实为JSON。
- `%echo`，可以用于输出环境变量，Windows环境下`%echo %PATH%`，Linux环境下`%echo $PATH`。
- `%automagic`，如果运行`%automagic off`，则输入魔法命令必须以`%`开头。
- `%run`，用来运行Python文件。
- `%load`，加载Python文件，可以从web中加载
- ​

我们还可以通过！来运行任意系统命令，如：

```
!ls
```

将列出目录下的当前文件，也可以使用`%ls`命令。

**2.文件转换**

将notebook转换为其它文件：

```shell
jupyter notebook "demo.ipynb"  # 将ipynb转化为html
jupyter notebook "demo.ipynb" --to slides # 将ipynb转换为slide html
```

**3.运行其它脚本**

可以使用`%% html`生成html，使用`%%javascript`运行javascript脚本，`%%python3`运行指定版本Python代码，其实可以通过`%script`来运行任意语言的脚本。

**4.显示图片**

IPython支持插入图片，视频和音频，使用下面的代码显示图片：

```python
from IPython.display import Image
Image('character.png')
```

也可以传递一个图片的url地址给Image对象。

使用下面的语句显示网页：

```python
from IPython.display import HTML
HTML(html_string)
```



