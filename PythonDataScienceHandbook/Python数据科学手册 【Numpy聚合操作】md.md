通常处理大规模的数据集时，首先要考虑的问题就是计算统计信息。也许用的最多的统计计算就是求平均值和标准方差了，不过其他的聚合操作也非常重要。

`Numpy` 提供了很多聚合操作的函数。

## 数组求和

Python提供了内置的函数用于计算数组中所有数值的和。

```python
import numpy as np
L = np.random.random(100)
sum(L)
# 52.374337843689133
```

`Numpy`提供的`sum`方法语法与之类似。

```
np.sum(L)
# 52.374337843689148
```

但是`Numpy`是在编译的代码上执行的，所以速度更快:

```python
big_array = np.random.rand(1000000)
%timeit sum(big_array)
%timeit np.sum(big_array)
```

结果为：

```
202 ms ± 6.47 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
1.75 ms ± 65.9 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

注意`sum`和`np.sum`是两个不同的函数，`np.sum`支持多维数组，`sum`并不支持。

