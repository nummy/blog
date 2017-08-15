### HTTP基本认证

HTTP基本认证分为HTTP Basic认证和HTTP Digest认证。

在HTTP中，HTTP基本认证是一种允许Web浏览器或者其他客户端在请求时提供**用户名**和**口令**形式的身份凭证的一种登录验证方式。

HTTP Digest是一种更加安全的认证形式， 在传输密码前会对其进行 MD5 哈希处理， 同时会配以密码随机数。 密码随机数是个随机数或伪随机数， 用于消息进行签名， 不过每个值只能使用一次。 由于每个随机数只会使用一次， 然后就会被标记为过期， 因此可以防止重放攻击（重新发送之前的密文)。

简单而言，HTTP基本认证就是我们平时在网站中最常用的通过用户名和密码登录来认证的机制。 

**优点** 
HTTP 基本认证是基本上所有流行的网页浏览器都支持。但是基本认证很少在可公开访问的互联网网站上使用，有时候会在小的私有系统中使用。 
**缺点** 
HTTP 基本认证虽然足够简单，但是前提是在客户端和服务器主机之间的连接足够安全。如果没有使用**SSL/TLS**这样的传输层安全的协议，那么以明文传输的密钥和口令很容易被拦截。 
由于现存的浏览器保存认证信息直到标签页或浏览器关闭，或者用户清除历史记录。导致了服务器端无法主动来当前用户登出或者认证失效。

### 开放授权(OAuth)

OAuth（Open Authorization，开放授权）是为用户资源的授权定义了一个安全、开放及简单的标准，第三方无需知道用户的账号及密码，就可获取到用户的授权信息，并且这是安全的。
OAuth 允许用户提供一个令牌，而不是用户名和密码来访问他们存放在特定服务提供者的数据。每一个令牌授权一个特定的网站（例如，视频编辑网站）在特定的时段（例如，接下来的2小时内）内访问特定的资源（例如仅仅是某一相册中的视频）。这样，OAuth让用户可以授权第三方网站访问他们存储在另外服务提供者的某些特定信息，而非所有内容。 
现存在`OAuth 1.0`和`OAuth 2.0`两种协议，二者互不兼容。 

**OAuth 2.0 运行流程**

![这里写图片描述](http://img.blog.csdn.net/20170314181148950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXE2NzMzMTg1MjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- （A）客户端（Client）向资源拥有者（Resource Owner）发送授权请求（Authorization Request）
- （B）资源拥有者授权许可（Authorization Grant）
- （C）客户端向验证服务器（Authorization Server）发送通过（B）获取的授权许可
- （D）验证服务器验证授权许可，通过的话，则返回Access Token给客户端
- （E）客户端通过Access Token 访问资源服务器（Resource Server）
- （F）资源服务器返回受保护的资源（Protected Resource）

这种基于OAuth的认证机制非常适用于个人消费者类的互联网应用产品，如社交类等等。一些大的互联网公司也都提供OAuth机制的API。

### Cookie/Session 认证机制

Cookie 是由客户端保存的小型文本文件，其内容为一系列的键值对。Cookie 是由 HTTP 服务器设置的，保存在浏览器中。Cookie会随着 HTTP请求一起发送。 
Session 是存储在服务器端的，避免在客户端 Cookie 中存储敏感数据。Session 可以存储在 HTTP 服务器的内存中，也可以存在内存数据库（如Redis中)。 
Cookie/Session认证机制就是为一次请求认证在服务端创建一个Session对象，同时在客户端的浏览器端创建了一个Cookie对象；通过客户端带上来Cookie对象来与服务器端的session对象匹配来实现状态管理的。默认的，当我们关闭浏览器的时候，cookie会被删除。但可以通过修改cookie 的expire time使cookie在一定时间内有效；

### 基于 Token 的认证机制

基于 Token的认证机制，有着无需长期保存用户名和密码，服务器端能主动让token失效等诸多好处，非常实用于 Web 应用和 App 已经被很多大型网站采用，比如Facebook、Github、Google+等。其流程如下： 

1. 客户端使用用户名和密码请求登录 
2. 服务端收到请求，去验证用户名与密码 
3. 验证成功后，服务器会签发一个 Token， 再把这个 Token 发送给客户端 
4. 客户端收到 Token 以后可以把它存储起来，如Cookie或者Web Storage 
5. 客户单每次向服务端请求资源的时候，都需要带着服务器端签发的 Token 
6. 服务器端收到请求，验证客户端请求里面带着的 Token，如果验证成功，就向客户端返回请求的数据；否的话，则返回对应的错误信息。

但是存储在客户端的 Token 存在几个问题： 

1. 存在泄露的风险。如果别人拿到你的 Token，在 Token过期之前，都可以以你的身份在别的地方登录 
2. 如果存在 Web Storage（指sessionStorage和localStorage）。由于Web Storage 可以被同源下的JavaScript直接获取到，这也就意味着网站下所有的Javascript代码都可以获取到web Storage，这就给了XSS机会 
3. 如果存在Cookie中。虽然存在Cookie可以使用`HttpOnly`来防止XSS，但是使用 Cookie 却又引发了CSRF

对于泄露的风险，可以采取对Token进行对称加密，用时再解密。 
对于XSS而言，在处理数据时，都应该 escape and encode 所有不信任的数据。 
与CSRF相比，XSS更加容易防范和意识到，因此并不建议将Token存在Cookie中。 
最后，网站或者应用一定要使用HTTPS。毕竟在网络层面上 Token 明文传输的话，还是非常的危险。