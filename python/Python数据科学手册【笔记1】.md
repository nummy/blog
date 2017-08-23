### IPython中的获取帮助与文档

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

### IPython快捷键

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