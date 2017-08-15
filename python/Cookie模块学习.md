Cookie模块定义了一些类用来解析和创建HTTP cookie头部字段。

### 1.创建和设置Cookie

Cookie通常由服务器端来设置，并由客户存储和返回。

```python
import Cookie

c = Cookie.SimpleCookie()
c["name"] = "jim"
print c.output()
```

输出结果为：

```Set-Cookie: name=jim```

输出是一个合法的`Set-Cookie`首部， 可以作为HTTP响应的一部分传递给客户端。

### 2.Morsel

还可以通过Cookie的Morsel对象来管理cookie的其他方面，比如到期时间，路径和域等。

```python
import Cookie
import datetime

c = Cookie.SimpleCookie()
c["name"] = "jim"
c["name"]["comment"] = "some comment"
c["name"]["path"] = "/sub/path"
c["name"]["domain"] = "python"
c["name"]["secure"] = True
# 法一，通过max-age设置到期时间
c["name"]["max-age"] = 300
now = datetime.datetime.now()
expires = now + datetime.timedelta(hours=1)
expires_at_time = expires.strftime("%a, %d %b %Y %H:%M:%S")
# 法二，通过expires设置到期时间
c["name"]["expires"] = expires_at_time
print c
```

输出结果为

```
Set-Cookie: name=jim; Comment=some comment; Domain=python; expires=Fri, 21 Jul 2017 12:34:53; Max-Age=300; Path=/sub/path; secure
```

Cookie实例的键是所存储的各个cookie的名称，Morsel对象实际就是cookie名称对应的值，它与字典类似。

### 3.编码值

cookie首部需要对值编码，才能正确解析。

```python
import Cookie

c = Cookie.SimpleCookie()
c["example"] = 'he said, "hello world "'
print c["example"].key
print c["example"].value
print c["example"].coded_value	
```

输出结果如下：

```
example
he said, "hello world "
"he said\054 \"hello world \""
```

由上可以看出，`Morsel.value`是cookie的解码值，而`Morsel.coded_value`表示用来将值传输到客户。

### 4.接收和解析Cookie首部

当客户端收到服务器端发送的`Set-Cookie`首部后，在后续请求中它会使用一个`Cookie`首部把这些cookie返回到服务器，到来的`Cookie`首部串可能包含了多个cookie值，由分号隔开。

取决于服务器和框架，可以直接从首部或者`HTTP_COOKIE`环境变量得到cookie，而解码cookie可以通过将串传递给`SimpleCookie`或者使用`load()`方法来实现。

```python
# coding:utf-8
import Cookie

HTTP_COOKIE = ";".join([r"name=jim", r"age=12"])

# 方法1
c1 = Cookie.SimpleCookie(HTTP_COOKIE)
print c1

# 方法2
c2 = Cookie.SimpleCookie()
c2.load(HTTP_COOKIE)
print c2
```

Cookie提供了三个不同的类`SimpleCookie`、`SerialCookie`和`SmartCookie`。它们的区别在于`SimpleCookie`只支持解析str类型的cookie，`SerialCookie`要求所有的值可序列化，`SmartCookie`两者都支持。

