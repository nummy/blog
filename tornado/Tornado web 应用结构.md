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

### `Application`对象

`Application`对象用于全局配置，包括路由映射，将请求转发至处理器。

路由表是由`URLSpec`对象组成的列表或元组。每个`URLSpec`包含了至少一个正则表达式和一个处理器类。路由表额顺序非常重要，第一个匹配的规则将会首先使用。如果正则表达式包含了捕获组，则这些捕获组将作为URL参数传递给处理器的HTTP方法。如果`URLSpec`提供了第三个参数--一个字典，则它将为`Request.initialize`方法提供参数。最后，`URLSpec`可以有一个`name`，它可以被`RequestHandhler.reverse_url`所使用。

例如，下面的代码中，根路由`/`将映射到`MainHandler`。而形式如 `/story/`后面接一个数字的URL将映射到`StoryHandler`。这个数字将传递给`StoryHandler.get`。

```python
class MainHandler(RequestHandler):
    def get(self):
        self.write('<a href="%s">link to story 1</a>' %
                   self.reverse_url("story", "1"))

class StoryHandler(RequestHandler):
    def initialize(self, db):
        self.db = db

    def get(self, story_id):
        self.write("this is story %s" % story_id)

app = Application([
    url(r"/", MainHandler),
    url(r"/story/([0-9]+)", StoryHandler, dict(db=db), name="story")
    ])
```

`Application`可以接收很多关键字参数，具体参考`Application.settings`。

### `RequestHandler`子类

Tornado的大部分工作都是通过`RequestHandler`的子类来实现。处理器子类的入口是以HTTP请求类型命名的方法：`get()`，`post()`等。每个处理器类可以定义一个或者多个HTTP请求方法，以处理不同的请求。

在处理器中，可以调用`RequestHandler.render`或者`RequestHandler.write`方法来产生一个响应。`render`加载模板并进行渲染，`write`直接生成一个输出，它可以接收字符串，字节数组或者字典。
`RequestHandler`类中的方法大部分都被设计为在子类中进行重载，这些方法在整个应用中都可以使用。通常可以写一个子类`BaseHandler`继承`RequestHandler`，子类重载方法`write_error`和`get_current_user`，然后在这个子类的基础上继续定义处理器子类。

### 处理用户输入

处理器可以通过`self.request`访问到当前的请求对象，可以查看`HTTPServerRequest`的定义查看更多属性。

HTML 表单提交的数据保存在请求对象中，可以通过`get_query_argument`或者`get_body_argument`来获取。

```python
class MyFormHandler(tornado.web.RequestHandler):
    def get(self):
        self.write('<html><body><form action="/myform" method="POST">'
                   '<input type="text" name="message">'
                   '<input type="submit" value="Submit">'
                   '</form></body></html>')

    def post(self):
        self.set_header("Content-Type", "text/plain")
        self.write("You wrote " + self.get_body_argument("message"))
```

由于HTML表单进行数据编码的时候，元素的值不能确定是否为列表。`RequestHandler`格外提供了方法`get_query_arguments`和`get_body_arguments`来获取元素为列表的输入信息。

上传的文件保存在`self.request.files`中，它是一个字典，key为文件名，value为file列表。每个file为一个字典，包含了`{"filename":..., "content_type":..., "body":...}` 等信息。只有当文件通过表单上传时，`request`才包含了`files`属性。否则，文件将保存在`self.request.body`中。默认情况下，文件缓存在内存中，如果上传的文件过大，可以考虑使用`stream_request_body`修饰器。

由于HTML表单的诡异性，Tornado默认不处理JSON数据，如果需要处理JSON编码的数据，可以重写`prepare`方法。

```python
def prepare(self):
    if self.request.headers["Content-Type"].startswith("application/json"):
        self.json_args = json.loads(self.request.body)
    else:
        self.json_args = None
```

### 重载请求方法

除了`get()`和`post()`方法，也可以在处理器子类中重载其他方法。一次完整的请求处理过程如下：

1. 创建一个`RequestHandler`对象
2. 调用`initialize()`方法，它使用`Application`的配置作为参数，该方法应该只用来保存参数，它不应该有任何输出，也不会调用`send_error()`方法。
3. 调用`prepare()`方法，它通常用于基类中，不管什么请求方法，它都会被调用，`prepare`方法可以产生输出，如果在方法中调用`finish`或者`redirect`，处理过程就会终止。
4. 调用HTTP请求处理方法：`get()`，`post()`， `put()`等。如果URL正则表达式包含了捕获组，则将捕获组传递给这些方法。
5. 请求结束之后，调用`on_finish()`方法，。如果是同步请求，它在`get()`等方法后面调用， 如果是异步请求，它在`finish`方法后面调用。

`RequestHandler`中常用的请求方法如下所示：

- **write_error** - 输出错误页面
- **on_connection_close** - 当连接中断的时候调用该方法，应用可以检查这种情况，并中断后续处理，注意中断的请求并不能立马检测到。
- **get_current_user** - 用于用户认证
- **get_user_local** - 返回当前用户的`Local`对象。
- **set_default_header** - 设置返回的头部信息。

### 错误处理

如果处理器抛出异常，Tornado将调用`RequestHandler.write_error`来生成一个错误页面。`Tornado.web.HTTPError`可以用于生成特殊的状态码，所有其它异常将返回500状态码。

默认的错误页面包含了调试模式下的堆栈跟踪信息和一行错误描述信息（例如：“500: Internal Server Error”）。如果需要自定制一个错误页面，可以重写`RequestHandler.write_error`方法。这个方法可以调用`write`或者`render`来生成一个错误页面。如果错误是异常导致的，一个三元组`exc_info`也会作为参数传递给该方法。

也可以从正常的请求中生成错误页面，只需要调用`set_status`，生成响应，然后返回即可。Tornado提供了一个特殊的异常`tornado.web.Finish`，它用于中断请求，而不会调用`write_error`，适用于不能有返回的情况。

对于404错误，使用`default_handler_class`，这个处理器应该重写`prepare`方法，以便能处理所有的HTTP请求方法，它还应该也生成一个错误页面：抛出一个`HttpError(404)`异常并重写`write_error`方法，或者调用`self.set_status(404)`然后直接通过`prepare()`方法生成响应。

### 重定向

在Tornado中两种方法可以重定向，`RequestHandler.redirect`和`RedirectHandler`。

我们可以使用`RequestHandler`的`self.redirect()`方法来重定向至其它请求，这个方法接收一个可选参数`permanent`用于指明这个重定向是否为永久重定向。默认为`False`，这时返回的响应码为`302`。如果`permanent`为`True`，则状态码为`301`。

通过`RedirectHandler`我们可以直接在应用的路由表中定义重定向，例如，配置静态重定向。

```python
app = tornado.web.Application([
    url(r"/app", tornado.web.RedirectHandler,
        dict(url="http://itunes.apple.com/my-app-id")),
    ])
```

`RedirectHandler`也支持正则表达式，下面的重定向配置将以`/pictures/`开头的`url`重定向至`/photos/`。

```python
app = tornado.web.Application([
    url(r"/photos/(.*)", MyPhotoHandler),
    url(r"/pictures/(.*)", tornado.web.RedirectHandler,
        dict(url=r"/photos/{0}")),
    ])
```

跟`RequestHandler.redirect`不同，`RedirectHandler`默认使用永久重定向。这是因为路由表不会在运行时修改，所以默认为永久重定向。而处理器中的重定向是可以改动的。如果需要通过`RequestHandler`来返回临时重定向，则需传递参数`permanent=False`给`RedirectHandler`。

### 异步处理器

Tornado的处理器默认是同步的，当`get()`或者`post()`方法返回的时候，请求就被认定为已结束，然后响应被发送给请求方。当一个处理器在处理请求时，其他请求都处于阻塞状态，所以对于执行时间比较长的任务都应该改为异步请求。

处理异步请求最简单的方式是使用`coroutine`修饰符，通过`yield`关键字我们可以执行非阻塞操作，响应信息要等协程执行完才会返回。

有些情况下，协程可能没有回调方便，这时可以使用`tornado.web.asynchronous`修饰器。当使用该修饰器时，响应不会自动发送，相反，请求会一直保持打开，直到回调调用`RequestHandler.finish`。应用程序决定这个方法是否需要调用，如过没有调用，则会挂住。

下面的代码使用回调获取API信息：

```python
class MainHandler(tornado.web.RequestHandler):
    @tornado.web.asynchronous
    def get(self):
        http = tornado.httpclient.AsyncHTTPClient()
        http.fetch("http://friendfeed-api.com/v2/feed/bret",
                   callback=self.on_response)

    def on_response(self, response):
        if response.error: raise tornado.web.HTTPError(500)
        json = tornado.escape.json_decode(response.body)
        self.write("Fetched " + str(len(json["entries"])) + " entries "
                   "from the FriendFeed API")
        self.finish()
```

当`get()`方法返回的时候，请求并没有结束，当HTTP client调用`on_repsonse`时，请求还是打开的，直到调用`self.finish()`方法，响应才被发送。

下面是使用协程的例子：

```python
class MainHandler(tornado.web.RequestHandler):
    @tornado.gen.coroutine
    def get(self):
        http = tornado.httpclient.AsyncHTTPClient()
        response = yield http.fetch("http://friendfeed-api.com/v2/feed/bret")
        json = tornado.escape.json_decode(response.body)
        self.write("Fetched " + str(len(json["entries"])) + " entries "
                   "from the FriendFeed API")
```

对于更复杂的例子，可以查看自带的聊天室例子，例子中使用了长轮询，使用长轮询需要重写`on_connection_close`方法。

