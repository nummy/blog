`js`，`css`更新的时候，由于浏览器的缓存机制，可能导致引用老的`js`，`css`而引起错误。以往常见的解决方法有两种：

1. 对js,css加版本控制，例如：`jquery.min.js?v=xx.xxx.xx`，此种方法存在的问题是需要对版本控制进行维护，比较麻烦。
2. 对`js`, `css`加时间戳， 此种方法存在的问题是当js或css，更改后又被还原，但是时间戳已经更改，而导致浏览器需要重新下载`js`，`css`。

今天要讲的是根据MD5来解决这个问题，它的工作原理很简单，根据js,css的内容生成一个md5字符串，当js,css发生改变的时候字符串也会随之更改。

Django提供的`CachedStaticFilesStorage`就是通过MD5解决这个问题的。

使用`CachedStaticFilesStorage`步骤如下:

修改`settings.py`：

```python
# 设置STATICFILES_STORAGE 
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.CachedStaticFilesStorage'
# 设置cache,memcache or redis(至少指定一个)
CACHES = {
    "default": {
        "BACKEND": "redis_cache.cache.RedisCache",
        "LOCATION": "127.0.0.1:6379:1",
        "OPTIONS": {
            "CLIENT_CLASS": "redis_cache.client.DefaultClient",
        }
    }
}
```

