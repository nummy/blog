`cookielib`一般用于客户端处理HTTP cookie信息，通过它可以从服务器端获取cookie信息，反过来又可以通过它将获取到的cookie发送给服务器。

`cookielib`提供了不同的类来自动处理HTTP的cookie信息，使用比较多的类包括了`CookieJar`、`MozillaCookieJar`以及`Cookie`。

### 打印cookie信息

```python
import urllib2
import cookielib

cookies = cookielib.CookieJar()
opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cookies))
res = opener.open("https://www.baidu.com")

for cookie in cookies:
    print "%s: %s" % (cookie.name, cookie.value)
```

输出结果如下：

```
BIDUPSID: 07AC167246820DCAEB9BB2F52AF46975
PSTM: 1500622668
BD_NOT_HTTPS: 1
```

上例中，打开网页之后，cookie信息都保存在CookieJar实例对象中，CookieJar实例是可迭代的，迭代的元素为Cookie对象，所以可以很方便获取每个cookie的详细信息。

### 保存cookie信息

下面的例子将cookie信息保存在文件中。

```python
# coding:utf-8
import urllib2
import cookielib

#设置保存cookie的文件
filename = 'cookie.txt'
#声明一个MozillaCookieJar对象来保存cookie，之后写入文件
cookie = cookielib.MozillaCookieJar(filename)
#创建cookie处理器
handler = urllib2.HTTPCookieProcessor(cookie)
#构建opener
opener = urllib2.build_opener(handler)
#创建请求
res = opener.open('http://www.baidu.com')
#保存cookie到文件
#ignore_discard的意思是即使cookies将被丢弃也将它保存下来
#ignore_expires的意思是如果在该文件中cookies已经存在，则覆盖原文件写入
cookie.save(ignore_discard=True,ignore_expires=True)
```

执行上面的程序，将在当前工作目录生成cookie.txt文件。

### 使用cookie模拟登陆

前面我们将cookie信息保存在文件中，后续如果想模拟这个用户进行登陆的话，只需要读取cookie信息，进行访问就行了：

```python
# coding:utf-8
import urllib2
import cookielib

#创建一个MozillaCookieJar对象
cookie = cookielib.MozillaCookieJar()
#从文件中的读取cookie内容到变量
cookie.load('cookie.txt',ignore_discard=True,ignore_expires=True)
#打印cookie内容,证明获取cookie成功
for item in cookie:
    print 'name:' + item.name + '-value:' + item.value
#利用获取到的cookie创建一个opener
handler = urllib2.HTTPCookieProcessor(cookie)
opener = urllib2.build_opener(handler)
res = opener.open('http://www.baidu.com')
print res.read()
```

