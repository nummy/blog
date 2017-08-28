### 获取帮助与文档

- 使用？获取帮助

  ```python
  In [2]: len?
  Type:        builtin_function_or_method
  String form: <built-in function len>
  Namespace:   Python builtin
  Docstring:
  len(object) -> integer

  Return the number of items of a sequence or mapping.
  ```

- 使用？？获取源码

  ```python
  In [9]: len??
  Type:        builtin_function_or_method
  String form: <built-in function len>
  Namespace:   Python builtin
  Docstring:
  len(object) -> integer

  Return the number of items of a sequence or mapping.
  ```

- 使用tab键补全命令

  ```python
  In [10]: import <TAB>
  Display all 399 possibilities? (y or n)
  Crypto              dis                 py_compile
  Cython              distutils           pyclbr
  ...                 ...                 ...
  difflib             pwd                 zmq

  In [10]: import h<TAB>
  hashlib             hmac                http         
  heapq               html                husl
  ```

- 通配符匹配

  ```python
  In [10]: str.*find*?
  str.find
  str.rfind
  ```

### 快捷键

- 导航快捷键

| 快捷键              | 操作          |
| ---------------- | ----------- |
| ``Ctrl-a``       | 将光标移至当前行的开头 |
| ``Ctrl-e``       | 将光标移至当前行的结尾 |
| ``Ctrl-b`` 或者左箭头 | 将光标向后移动一个字符 |
| ``Ctrl-f`` 或者右箭头 | 将光标向前移动一个字符 |

- 文本操作快捷键

| 快捷键        | 操作          |
| ---------- | ----------- |
| 回车键        | 删除前一个字符     |
| ``Ctrl-d`` | 删除后一个字符     |
| ``Ctrl-k`` | 删除从光标到行尾的字符 |
| ``Ctrl-u`` | 删除从行头到光标的字符 |
| ``Ctrl-y`` | 粘贴之前剪切的文本   |
| ``Ctrl-t`` | 交换前面的两个字符   |

- 历史命令快捷键

| 快捷键                | 操作        |
| ------------------ | --------- |
| ``Ctrl-p`` (或者上箭头) | 获取上一条历史命令 |
| ``Ctrl-n`` (或者下箭头) | 获取下一条历史命令 |
| ``Ctrl-r``         | 反向搜索历史命令  |

- 其它命令

| 快捷键        | 操作          |
| ---------- | ----------- |
| ``Ctrl-l`` | 清空          |
| ``Ctrl-c`` | 中断当前命令      |
| ``Ctrl-d`` | 退出IPython会话 |

### 魔法命令

- 粘贴代码`%paste`或者`%cpaste`，使用该命令粘贴多行代码。
- 执行系统命令`%run`
- 统计代码费时`%timeit`
- `%magic`获取所有魔法命令的详细信息
- `%lsmagic`获取所有魔法命令列表

### 输入输出

输入和输出通过`In/Out`标签标示，实际上`In`和`Out`分别是两个变量，保存了最近的输入输出信息。可以直接通过下标获取之前的输入或者输出。

```python
In [4]: print(In)
['', 'import math', 'math.sin(2)', 'math.cos(2)', 'print(In)']

In [5]: Out
Out[5]: {2: 0.9092974268256817, 3: -0.4161468365471424}
In [6]: print(In[1])
import math
```

还可以通过`_`获取前一个输出结果，`__`获取倒数第二个输出结果，`___`获取倒数第三个输出结果。除此之外，也可以通过`_x`+序号获取第x个输出结果。

在命令后面加`;`可以隐藏输出结果。

`IPython`也提供了魔法命令`%history`来获取历史输入。

```
In [16]: %history -n 1-4
   1: import math
   2: math.sin(2)
   3: math.cos(2)
   4: print(In)
```

### shell命令

可以在`IPython`中直接运行shell命令， 只要在命令前面加上`!`即可。也可以将shell命令的结果传递给Python变量。如果需要将Python变量传递给shell，将变量用`{}`括起来即可。

```python
In [4]: contents = !ls

In [5]: print(contents)
['myproject.txt']

In [9]: message = "hello from Python"
In [10]: !echo {message}
hello from Python
```

### 异常和调试

- 控制异常信息的输出

  魔术命令`%xmode`可以控制异常信息的输出。它可以接受一个参数，参数可选值为`Plain`，`Context`，`Verbose`，默认值为`Context`，`Plain`更为紧凑，而`Verbose`比较详细。

- 调试

  当发生异常信息时，可以通过`%debug`进行调试，输入该命令之后，会进入`ipdb`。

  | Command        | Description                              |
  | -------------- | ---------------------------------------- |
  | ``list``       | Show the current location in the file    |
  | ``h(elp)``     | Show a list of commands, or find help on a specific command |
  | ``q(uit)``     | Quit the debugger and the program        |
  | ``c(ontinue)`` | Quit the debugger, continue in the program |
  | ``n(ext)``     | Go to the next step of the program       |
  | ``<enter>``    | Repeat the previous command              |
  | ``p(rint)``    | Print variables                          |
  | ``s(tep)``     | Step into a subroutine                   |
  | ``r(eturn)``   | Return out of a subroutine               |