这一节我们将讨论使用布尔值掩码来操作数组， 掩码可以用于执行提取，修改，计数等操作。

## 示例

假设有一个数据集，保存了每个城市在一年中每天的降雨量，下面使用pandas读取西雅图在2014年的降雨量统计数据。

```python
import numpy as np
import pandas as pd

# use pandas to extract rainfall inches as a NumPy array
rainfall = pd.read_csv('data/Seattle2014.csv')['PRCP'].values
inches = rainfall / 254.0  # 1/10mm -> inches
inches.shape
```

这个数组包含了365个数值，代表了2014年1月1号到12月31号的降雨量数据。

```python
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn; seaborn.set()  # set plot styles
```

绘制直方图：

```python
plt.hist(inches, 40);
```

![](./img/numpy_hist.png)

直方图大概描述了数据长什么样子，大部分数据都分布在0附近，但是这并没有很好的展示我们想要的信息，比如一年中有多少天是雨天？雨天的平均降雨量是多少？有多少雨天的降水量超过了半英尺？

### 挖掘数据

一种解决办法是遍历数据，使用一个计数器统计信息，但是这样做得效率非常低。另一种方法是使用`Numpy`提供的掩码功能。

## 通用函数中的比较运算符

`Numpy`也支持比较操作运算，例如`>`或者`<`等。比较操作的结果是一个布尔数组，所有的标准比较运算都是支持的。

```python
x = np.array([1, 2, 3, 4, 5])
x < 3  # less than
# array([ True,  True, False, False, False], dtype=bool)
x > 3  # greater than
# array([False, False, False,  True,  True], dtype=bool)
x <= 3  # less than or equal
# array([False, False,  True,  True,  True], dtype=bool)
x != 3  # not equal
# array([ True,  True, False,  True,  True], dtype=bool)
x == 3  # equal
# array([False, False,  True, False, False], dtype=bool)
```

也可以在比较操作中使用组合表达式：

```
(2 * x) == (x ** 2)
# (2 * x) == (x ** 2)
```

`Numpy`也提供了对应的比较函数，如下表所示：

| 操作符    | 等价函数           |      | 操作符    | 等价函数                 |
| ------ | -------------- | ---- | ------ | -------------------- |
| ``==`` | ``np.equal``   |      | ``!=`` | ``np.not_equal``     |
| ``<``  | ``np.less``    |      | ``<=`` | ``np.less_equal``    |
| ``>``  | ``np.greater`` |      | ``>=`` | ``np.greater_equal`` |

比较操作同样支持多维数组：

```python
rng = np.random.RandomState(0)
x = rng.randint(10, size=(3, 4))
x < 6
```

结果是一个多维的布尔数组。

## 使用布尔数组

