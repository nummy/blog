Tornado web应用的结构通常包含一个或者多个`RequestHandler`子类，一个将请求转发至处理器的`Application`对象，以及一个`main()`函数，用于启动服务器。

一个简单的hello,world 程序示例代码如下：

```python
import tornado.ioloop
import tornado.web

class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write("Hello, world")

def make_app():
    return tornado.web.Application([
        (r"/", MainHandler),
    ])

if __name__ == "__main__":
    app = make_app()
    app.listen(8888)
    tornado.ioloop.IOLoop.current().start()
```

### Application对象

`Application`对象用于全局配置，包括路由映射，将请求转发至处理器。

路由表是由`URLSpec`对象组成的列表或元组。每个`URLSpec`包含了至少一个正则表达式和一个处理器类。路由表额顺序非常重要，第一个匹配的规则将会首先使用。如果正则表达式包含了捕获组，则这些捕获组将作为URL参数传递给处理器的HTTP方法。如果`URLSpec`提供了第三个参数--一个字典，则它将为`Request.initialize`方法提供参数。最后，`URLSpec`可以有一个`name`，它可以被`RequestHandhler.reverse_url`所使用。

例如，下面的代码中，根路由`/`将映射到`MainHandler`。