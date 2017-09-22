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

## 最小值和最大值

Python提供了函数`min()`和`max()`用于计算最小值和最大值。

```
min(big_array), max(big_array)
# (1.1717128136634614e-06, 0.9999976784968716)
```

`Numpy`提供了类似的语法，但是计算速度更快：

```python
np.min(big_array), np.max(big_array)
# %timeit min(big_array)
%timeit np.min(big_array)
```

```
%timeit min(big_array)
%timeit np.min(big_array)
```

结果为：

```
10 loops, best of 3: 82.3 ms per loop
1000 loops, best of 3: 497 µs per loop
```

对于`max`，`min` ，`sum`等聚合操作，更简短的语法是直接调用数组对象的对应方法。

```python
print(big_array.min(), big_array.max(), big_array.sum())
```

 结果为：

```
1.17171281366e-06 0.999997678497 499911.628197
```

不管什么情况下，注意使用`Numpy`提供的聚合函数。

### 多维聚合

常见的聚合操作包括横向聚合和纵向聚合，建设存在二维数组:

```python
M = np.random.random((3, 4))
print(M)
```

结果为：

```python
[[ 0.37550702  0.93955841  0.57002704  0.39057605]
 [ 0.90320865  0.20625684  0.97836493  0.21819763]
 [ 0.34062068  0.18802067  0.65719458  0.74388148]]
```

默认情况下，每个聚合函数都是针对整个数组进行的：

```python
M.sum()
# 6.511413969989138
```

聚合函数可以接收一个格外的参数，用于指明沿哪条轴进行聚合。例如，`axis=0`表明沿纵轴方向：

```python
M.min(axis=0)
```

结果为：

```
array([ 0.34062068,  0.18802067,  0.57002704,  0.21819763])
```

该函数返回四个值，分别对应每一列的最小值，同样的，也可以查看每一行的最大值：

```
M.max(axis=1)
```

结果为：

```python
array([ 0.93955841,  0.97836493,  0.74388148])
```

这里的`axis`指明了数组维度崩塌的方向，而不是返回的维度。

### 其它聚合函数

`Numpy`还提供了其他聚合函数，这里不再细述。每个聚合函数都对应提供一个对于`NaN`安全的函数，这些函数会自动忽略丢失的值(即`NaN`)。

下表列出了常见的`Numpy聚合函数`：

| 函数名               | 安全函数                 | 描述                                       |
| ----------------- | -------------------- | ---------------------------------------- |
| ``np.sum``        | ``np.nansum``        | 求和                                       |
| ``np.prod``       | ``np.nanprod``       | 求乘积                                      |
| ``np.mean``       | ``np.nanmean``       | 求平均值                                     |
| ``np.std``        | ``np.nanstd``        | 求标准差                                     |
| ``np.var``        | ``np.nanvar``        | 求方差                                      |
| ``np.min``        | ``np.nanmin``        | 求最小值                                     |
| ``np.max``        | ``np.nanmax``        | 求最大值                                     |
| ``np.argmin``     | ``np.nanargmin``     | 找出最小值的索引                                 |
| ``np.argmax``     | ``np.nanargmax``     | 找出最大值的索引                                 |
| ``np.median``     | ``np.nanmedian``     | 求中位数                                     |
| ``np.percentile`` | ``np.nanpercentile`` | Compute rank-based statistics of elements |
| ``np.any``        | N/A                  | 判断数组中是否有元素为True                          |
| ``np.all``        | N/A                  | 判断数组中是否所有元素都为True                        |

