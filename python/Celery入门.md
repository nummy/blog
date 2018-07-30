### 应用

Celery库在使用之前必须进行实例化操作，这个实例称之为应用。应用时线程安全，所以可以同时运行多个Celery应用。

创建应用:

```
>>> from celery import Celery
>>> app = Celery()
>>> app
<Celery __main__:0x100469fd0>
```

