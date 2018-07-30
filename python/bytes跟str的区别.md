在python3中，字符序列有两种表示方式：

- bytes： 由8比特的值组成
- str：由unicode字符组成

在python2中恰恰相反，它的字符附列有以下两种方式：

- str：由8比特的值组成
- unicode：由unicode字符组成

将unicode字符表示为二进制数据有多种方式，最常用的编码是使用UTF-8。通过encode方法将unicode编码成二进制数据，通过decode方法将二进制数据解码成unicode字符。

在编写程序的过程中尤其要注意这种区别，尽量保证代码使用同一种编码。

在python3中可以使用以下方法确保得到的字符序列是指定的类型：

```python
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode(‘utf-8’)
    else:
        value = bytes_or_str
    return  value

def to_bytes(bytes_or_str):
    if isinstance(bytes_or_str, str):
        value = bytes_or_str.encode(‘utf-8’)
    else:
        value = bytes_or_str
    return value
```

在python2中可以使用以下方法确保得到的字符序列是指定的类型：

```python
def to_unicode(unicode_or_str):
    if isinstance(unicode_or_str, str):
        value = unicode_or_str.decode(‘utf-8’)
    else:
        value = unicode_or_str
    return  value

def to_str(unicode_or_str):
    if isinstance(unicode_or_str, unicode):
        value = unicode_or_str.encode(‘utf-8’)
    else:
        value = unicode_or_str
    return value
```