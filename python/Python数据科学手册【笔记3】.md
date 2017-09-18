[TOC]

Pandas是基于`Numpy`的基础上构建的，它提供了高效的`DataFrame`，`DataFrame`是一个多维数组，但是行和列都绑定了标签。

### 一.安装和使用

安装：

```bash
$ pip install pandas
```

使用

```python
import pandas
pandas.__version__
```

通常使用下面的方式导入`pandas`：

```python
import pandas as pd
```

### 二.Pandas 对象简介

Pandas对象可以理解为`Numpy`的增强版， 也就是行和列都绑定了标签。`Pandas`提供了很多实用的对象和工具函数。

Pandas引入以下三种基本数据结构：

- `Series`
- `DataFrame`
- `Index`

#### 1.Series 对象

Pandas Series 对象是一个一维的数组， 带有索引的数据。它可以从列表或者数组中创建。

```python
data = pd.Series([0.25, 0.5, 0.75, 1.0])
print(data)
```

输出结果如下：

```
0    0.25
1    0.50
2    0.75
3    1.00
dtype: float64
```

从结果中可以看出，Series对象包含了值序列和索引序列，这些都可以通过`values`和`index`属性获取， `values`实际上就是一个`Numpy`数组。

```
print(data.values)
```

输出结果为：

```shell
array([ 0.25,  0.5 ,  0.75,  1.  ])
```

`index`返回的是一个类数组对象`pd.Index`:

```python
print(data.index)
```

输出为:

```python
RangeIndex(start=0, stop=4, step=1)
```

跟`Numpy`数组一样，数据可以直接通过索引来获取：

```python
print(data[1])
# 0.5
```

也可以使用切片：

```python
data[1:3]
```

结果为：

```shell
1    0.50
2    0.75
dtype: float64
```

Pandas Series 与`Numpy`的一个区别是，`Numpy`只支持隐式声明索引，而Series却支持显示索引声明。显示声明索引给Series提供了额外的功能。例如，索引不一定非得是数字，可以是任意数据类型。

```python
data = pd.Series([0.25, 0.5, 0.75, 1.0],
                 index=['a', 'b', 'c', 'd'])
data
'''
a    0.25
b    0.50
c    0.75
d    1.00
dtype: float64
'''
```

然后通过索引获取数据：

```python
data['b']
# 0.5
```

还可以创建非连续的索引：

```python
data = pd.Series([0.25, 0.5, 0.75, 1.0],
                 index=[2, 5, 3, 7])
data
'''
2    0.25
5    0.50
3    0.75
7    1.00
dtype: float64
'''
```

`Series`也可以通过字典来创建，只不过字典的key必须为同一数据类型，value也必须为同一数字类型。

```python
population_dict = {'California': 38332521,
                   'Texas': 26448193,
                   'New York': 19651127,
                   'Florida': 19552860,
                   'Illinois': 12882135}
population = pd.Series(population_dict)
population
​```
California    38332521
Florida       19552860
Illinois      12882135
New York      19651127
Texas         26448193
dtype: int64
​```
```

然后可以像字典一样获取数据：

```python
population['California']
# 38332521
```

不过与字典不同的是，Series还支持类似数组的操作，比如说切片：

```python
population['California':'Illinois']
'''
California    38332521
Florida       19552860
Illinois      12882135
dtype: int64
'''
```

创建Series对象， 使用下面的语法创建Series对象。

```python
>>> pd.Series(data, index=index)
```

index是可选参数，data参数可以为列表也可以为数组。

```python
pd.Series([2, 4, 6])
```

data还可以是一个标量，这种情况下所有的值都是一样的。

```python
pd.Series(5, index=[100, 200, 300])
```

data还可以为字典，索引默认为排序之后的key值

```python
pd.Series({2:'a', 1:'b', 3:'c'})
```

#### 2.DataFrame对象

可以将DataFrame视为由Series对象组成的序列。所以，我们再创建一个Series， 然后创建DataFrame

```python
area_dict = {'California': 423967, 'Texas': 695662, 'New York': 141297,
             'Florida': 170312, 'Illinois': 149995}
area = pd.Series(area_dict)
states = pd.DataFrame({'population': population,
                       'area': area})
```

从字典列表创建：

```python
data = [{'a': i, 'b': 2 * i}
        for i in range(3)]
pd.DataFrame(data)
```

|      | a    | b    |
| ---- | ---- | ---- |
| 0    | 0    | 0    |
| 1    | 1    | 2    |
| 2    | 2    | 4    |

如果部分键丢失，Pandas会填充为NaN

```python
pd.DataFrame([{'a': 1, 'b': 2}, {'b': 3, 'c': 4}])
```

|      | a    | b    | c    |
| ---- | ---- | ---- | ---- |
| 0    | 1.0  | 2    | NaN  |
| 1    | NaN  | 3    | 4.0  |



