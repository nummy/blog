实时Web应用通常需要针对每个用户创建持久连接，对于传统的同步服务器，这意味着需要给每个用户单独创建一个线程，这样做得代价非常高。

为了减少并发连接的消耗，Tornado采用了单线程事件循环模型，这也就意味着所有的应用代码都必须是异步非阻塞的，因为一次只能有一个活跃的操作。

异步和非阻塞其实紧密关联，通常它们可以互换，但是它们并不是同一个东西。

### 阻塞

当函数需要等待某件事情的发生并返回结果时，它就处于阻塞状态。一个函数可能因为很多原因阻塞，网络IO，磁盘IO， 互锁等等。实际上，每个函数都会阻塞，至少有那么一会，当它运行并占用CPU的时候。

函数可能有些情况会阻塞，有些情况又不会阻塞。例如，`tornado.httpclient`在采用默认配置的情况下，解析DNS的时候会阻塞，但其它网络访问并不会阻塞。在Tornado中，我们谈到的阻塞一般是针对网络IO，而忽略其它的阻塞。

### 异步

异步函数在结束之前就返回了，它通常在后台触发一些任务，等执行完之后再调用某些操作。有很多异步接口的实现：

- 回调参数
- 返回一个占位符(`Future`, `Promise`,` Defered`)
- 传送给队列
- 注册回调函数

不管采用哪种异步接口，异步函数与调用者的交互都不是同步的。

### 示例

下面是一个同步函数：

```python
from tornado.httpclient import HTTPClient

def synchronous_fetch(url):
    http_client = HTTPClient()
    response = http_client.fetch(url)
    return response.body
```

下面是使用回调参数改写成异步函数的版本：

```python
from tornado.httpclient import AsyncHTTPClient

def asynchronous_fetch(url, callback):
    http_client = AsyncHTTPClient()
    def handle_response(response):
        callback(response.body)
    http_client.fetch(url, callback=handle_response)
```

使用`Future`替换回调：

```python
from tornado.concurrent import Future

def async_fetch_future(url):
    http_client = AsyncHTTPClient()
    my_future = Future()
    fetch_future = http_client.fetch(url)
    fetch_future.add_done_callback(
        lambda f: my_future.set_result(f.result()))
    return my_future
```

原始的`Future`版本更为复杂，尽管如此，还是推荐在Tornado中使用`Future`，因为它有两个优点：

- 错误处理更为一致，因为`Future.result`可以抛出异常。
- `Future`在协程中使用非常方便。

协程在后面会重点介绍，下面是采用协程方式编写的代码：

```python
from tornado import gen

@gen.coroutine
def fetch_coroutine(url):
    http_client = AsyncHTTPClient()
    response = yield http_client.fetch(url)
    raise gen.Return(response.body)
```

使用`raise gen.Return(response.body)`是为了兼容Python2，因为Python2中生成器不允许返回值，为了克服这一点，Tornado协程抛出了一种特殊的异常`Return`，协程会捕获这个异常，然后将它当做返回值处理，在Python3中，可以直接使用`return response.body`