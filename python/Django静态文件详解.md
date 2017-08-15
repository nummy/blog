Django通过`django.contrib.staticfiles`来管理静态文件。

### 配置静态文件

- 首先确保`django.contrib.staticfiles`已经添加到`INSTALLED_APPS`

- 在配置文件中设置`STATIC_URL`，例如`STATIC_URL = '/static/'`

- 在模板中可以通过两种方式来引用静态文件，一是直接硬编码，例如`/static/my_app/example.jpg`。二是使用`static`模板标签：

  ```html
  {% load static %}
  <img src="{% static "my_app/example.jpg" %}" alt="My image"/>
  ```

- 将静态文件保存在应用的`static`目录下。

### 开发环境

在开发环境下，当通过`runserver`启动并且`debug=True`的时候，服务器会自动转发静态文件，但是这样做效率并不高，也不安全，不适合生产环境。

如果你不想将静态文件放在应用的目录下，而是统一放在某个目录下，可以通过`STATICFILES_DIRS`进行配置：

```python
STATICFILES_DIRS = [
	os.path.join(BASE_DIR, "static"),
	'/var/www/static/',
]
```

### 生产环境

`django.contrib.staticfiles`提供了一个很好的命令用来收集所有的静态文件，并统一放在一个目录下面。

- 设置`STATIC_ROOT`，用来保存最终的静态文件， 例如：

  ```
  STATIC_ROOT = "/var/www/example.com/static/"
  ```

- 运行`collectstatic`命令，执行下面的命令会将所有的静态文件都拷贝到`STATIC_ROOT`目录下。

  ```shell
  $ python manage.py collectstatic
  ```

一般的，生产环境不会通过django来转发静态文件，而是通过其他服务器进行转发，比如nginx，apache等。

### STATIC_ROOT和STATIC_URL的区别

`STATIC_ROOT`用来保存收集到的静态文件，服务器最终也将从该路径中获取文件进行转发。

`STATIC_URL`用来引用静态文件，也就是渲染之后HTML中静态文件的前缀。