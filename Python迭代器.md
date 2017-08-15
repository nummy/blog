迭代器(iterator)在Python中应用非常广泛，解析式和生成器只是最简单的迭代器。通过yield来构建迭代器也是一种常见的形式。

首先，我们先使用普通的方式构建一个`fibonacci`迭代器：

```python
class Fib:
    '''iterator that yields numbers in the Fibonacci sequence'''

    def __init__(self, max):
        self.max = max

    def __iter__(self):
        self.a = 0
        self.b = 1
        return self

    def __next__(self):
        fib = self.a
        if fib > self.max:
            raise StopIteration
        self.a, self.b = self.b, self.a + self.b
        return fib

```

