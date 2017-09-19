### 获取帮助

```
$ pip install line_profiler获取帮助与文档
```

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

  | 命令             | 描述           |
  | -------------- | ------------ |
  | ``list``       | 展示当前行在文件中的位置 |
  | ``h(elp)``     | 查看帮助命令       |
  | ``q(uit)``     | 退出调试         |
  | ``c(ontinue)`` | 退出调试，继续执行    |
  | ``n(ext)``     | 执行下一步        |
  | ``<enter>c     | 重复上一步        |
  | ``p(rint)``    | 打印变量         |
  | ``s(tep)``     | 进入子程序        |
  | ``r(eturn)``   | 退出子程序        |

### 性能测试

`IPython`提供了以下魔法命令用于性能测试：

- `%time`测试单条语句的运行时间
- `%timeit` 重复执行多次单条语句以获取更为精确的时间
- `%prun`使用profiler运行代码
- `%lprun`使用profiler逐行执行代码
- `%memit`测试单条语句的内存使用情况
- `%mprun`使用memory profiler逐行运行代码

后面四条命令需要安装`line_profiler`和 `memory_profiler` 扩展。

通常`%timeit`的执行速度比`%time`要快，因为它做了一些优化，可以省去部分垃圾回收。

- 使用`%prun`

  首先定义一个函数：

  ```python
  def sum_of_lists(N):
      total = 0
      for i in range(5):
          L = [j ^ (j >> i) for j in range(N)]
          total += sum(L)
      return total
  ```

  接下来调用命令`%prun`

  ```
  %prun sum_of_lists(1000000)
  ```

  输出如下：

  ```
  14 function calls in 0.714 seconds

     Ordered by: internal time

     ncalls  tottime  percall  cumtime  percall filename:lineno(function)
          5    0.599    0.120    0.599    0.120 <ipython-input-19>:4(<listcomp>)
          5    0.064    0.013    0.064    0.013 {built-in method sum}
          1    0.036    0.036    0.699    0.699 <ipython-input-19>:1(sum_of_lists)
          1    0.014    0.014    0.714    0.714 <string>:1(<module>)
          1    0.000    0.000    0.714    0.714 {built-in method exec}
  ```

  从结果中可以看出，`listcomp`的费时最长，从而我们可以知道要对他进行优化。

- 逐行调试`%lprun`

  先安装以下库

  ```shell
  $ pip install line_profiler
  ```

  然后载入`line_profiler`模块

  ```python
  %load_ext line_profiler
  ```

  执行以下代码：

  ```
  %lprun -f sum_of_lists sum_of_lists(5000)
  ```

  得到结果如下：

  ```
  Timer unit: 1e-06 s

  Total time: 0.009382 s
  File: <ipython-input-19-fa2be176cc3e>
  Function: sum_of_lists at line 1

  Line #      Hits         Time  Per Hit   % Time  Line Contents
  ==============================================================
       1                                           def sum_of_lists(N):
       2         1            2      2.0      0.0      total = 0
       3         6            8      1.3      0.1      for i in range(5):
       4         5         9001   1800.2     95.9          L = [j ^ (j >> i) for j in range(N)]
       5         5          371     74.2      4.0          total += sum(L)
       6         1            0      0.0      0.0      return total
  ```

- 使用`%memit`和`%mprun`

  先安装库：

  ```
  $ pip install memory_profiler
  ```

  加载库

  ```
  %load_ext memory_profiler
  ```

  运行命令`%memit`

  ```
  %memit sum_of_lists(1000000)
  ```

  结果输出为：`peak memory: 100.08 MiB, increment: 61.36 MiB`，可以看出该函数大概使用了100M的内存。

  使用`%mprun`逐行测试内存使用情况，但是该命令并不支持直接测试notebook中的代码，而需要从模块中导入才能进行测试。

  所以先创建模块：

  ```python
  %%file mprun_demo.py
  def sum_of_lists(N):
      total = 0
      for i in range(5):
          L = [j ^ (j >> i) for j in range(N)]
          total += sum(L)
          del L # remove reference to L
      return total
  ```

  接下来逐行测试：

  ```python
  from mprun_demo import sum_of_lists
  %mprun -f sum_of_lists sum_of_lists(1000000)
  ```

  结果如下所示：

  ```
  Filename: ./mprun_demo.py

  Line #    Mem usage    Increment   Line Contents
  ================================================
       1     39.0 MiB      0.0 MiB   def sum_of_lists(N):
       2     39.0 MiB      0.0 MiB       total = 0
       3     46.5 MiB      7.5 MiB       for i in range(5):
       4     71.9 MiB     25.4 MiB           L = [j ^ (j >> i) for j in range(N)]
       5     71.9 MiB      0.0 MiB           total += sum(L)
       6     46.5 MiB    -25.4 MiB           del L # remove reference to L
       7     39.1 MiB     -7.4 MiB       return total
  ```


