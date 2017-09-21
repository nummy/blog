Python中的数据操作几乎等价于Numpy数组操作，即使像Pandas这样的工具也是在Numpy的基础上构建的。这一节将讲述Numpy数组的以下基本操作。

- 数组的属性
- 数组索引
- 数组切片
- 数组变换
- 合并和切割数组

## 数组属性

首先讨论一些比较重要的数组属性，分别定义三个随机数组，一个一维，一个二维，一个三维。这里使用了seed以确保每次运行生成的数组都是一样的。

```python
import numpy as np
np.random.seed(0)  # seed for reproducibility

x1 = np.random.randint(10, size=6)  # One-dimensional array
x2 = np.random.randint(10, size=(3, 4))  # Two-dimensional array
x3 = np.random.randint(10, size=(3, 4, 5))  # Three-dimensional array
```

每个数组都有一个`ndim`属性（它指定了数组的维度），`shape`属性(它指定每个维度的大小)，以及`size`属性（它指明数组的总的大小）

```python
print("x3 ndim: ", x3.ndim)
print("x3 shape:", x3.shape)
print("x3 size: ", x3.size)
```

结果如下：

```
x3 ndim:  3
x3 shape: (3, 4, 5)
x3 size:  60
```

另外一个有用的属性是`dtype`，它指明了数组中元素的数据类型。

```python
print("dtype:", x3.dtype)
# dtype: int64
```

其他属性包括了`itemsize`（它列出每个数据元素占用的空间大小（单位为比特）），`nbytes`列出数组占用空间总的大小。

```python
print("itemsize:", x3.itemsize, "bytes")
print("nbytes:", x3.nbytes, "bytes")
```

结果为：

```
itemsize: 8 bytes
nbytes: 480 bytes
```

`nbytes`实际等于`itemsize` 与`size`的乘积。

## 数组索引

如果你已经熟悉了Python的标准列表索引，那么使用`Numpy`数组索引实际上非常类似。对于一维数组，索引方式与Python列表一致。

```python
x1    # array([5, 0, 3, 3, 7, 9])
x1[0] # 5
x1[4] # 7
x1[-1] # 9
x1[-2] # 7
```

对于多维数组，数据元素可以通过二元元组的方式索引：

```python
x2 
'''
array([[3, 5, 2, 4],
       [7, 6, 8, 8],
       [1, 6, 7, 7]])
'''
x2[0, 0]  # 3
x2[2, 0]  # 1
x2[2, -1] # 7
```

数组中的元素也可以通过索引进行修改。

```python
x2[0, 0] = 12
x2
```

结果为：

```
array([[12,  5,  2,  4],
       [ 7,  6,  8,  8],
       [ 1,  6,  7,  7]])
```

时刻记住，`Numpy`数组中元素的数据类型必须一致，如果修改的时候可以向上转型则向上转型。

```python
x1[0] = 3.14159  # this will be truncated!
x1   # array([3, 0, 3, 3, 7, 9])
```

## 数组切片

数组切片用于获取子数组，`Numpy`中的数组切片与Python标准的列表切片类似。使如下语法格式

```
x[start:stop:step]
```

### 一维数组切片

```python
x = np.arange(10)   # array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
x[:5]  # first five elements array([0, 1, 2, 3, 4])
x[5:]  # elements after index 5 array([5, 6, 7, 8, 9])
x[4:7]  # middle sub-array array([4, 5, 6])
x[::2]  # every other element array([0, 2, 4, 6, 8])
x[1::2]  # every other element, starting at index 1 array([1, 3, 5, 7, 9])
```

当step的值为负的时候，相当于start和stop的值互相交换了，可以用来倒排数组。

```python
x[::-1]  # all elements, reversed  array([9, 8, 7, 6, 5, 4, 3, 2, 1, 0])
x[5::-2]  # reversed every other from index 5  array([5, 3, 1])
```

### 多维数组切片

多维数组切片工作原理类似，只不过使用逗号分隔多个切片。

```python
x2
'''
array([[12,  5,  2,  4],
       [ 7,  6,  8,  8],
       [ 1,  6,  7,  7]])
'''
x2[:2, :3]  # two rows, three columns
'''
array([[12,  5,  2],
       [ 7,  6,  8]])
'''
x2[:3, ::2]  # all rows, every other column
'''
array([[12,  2],
       [ 7,  8],
       [ 1,  7]])
'''
```

整个多维数组也可以倒排：

```python
x2[::-1, ::-1]
'''
array([[ 7,  7,  6,  1],
       [ 8,  8,  6,  7],
       [ 4,  2,  5, 12]])
'''
```

### 获取多维数组的行或者列

有时候需要获取多维数据的某一行或者某一列，这是可以通过组合索引和切片来实现。

```python
print(x2[:, 0])  # first column of x2  [12  7  1]
print(x2[0, :])  # first row of x2 [12  5  2  4]
```

如果只是想获取某一行，可以使用更为简便的语法：

```
print(x2[0])  # equivalent to x2[0, :]  [12  5  2  4]
```

### 数组视图

注意数组切片返回的子数组都只是原数组的视图，而不是复制数组的数据。这是`Numpy`切片和Python列表切片的不同点之一。对于Python列表切片，会直接复制数据。考虑下面的二维数组：

```python
print(x2)
```

输出如下：

```
[[12  5  2  4]
 [ 7  6  8  8]
 [ 1  6  7  7]]
```

从中提取一个2x2子数组：

```
x2_sub = x2[:2, :2]
print(x2_sub)
```

输出如下：

```
[[12  5]
 [ 7  6]]
```

下面修改这个子数组，注意原数组也会修改。

```python
x2_sub[0, 0] = 99
print(x2_sub)
'''
[[99  5]
 [ 7  6]]
'''
print(x2)
'''
[[99  5  2  4]
 [ 7  6  8  8]
 [ 1  6  7  7]]
'''
```

这种默认的行为实际上非常有用，这意味着当我们处理大型数组的时候，可以先获取部分数据进行操作。

### 复制数组

尽管数组视图非常有用，但是有时候还是想复制整个数组或者子数组，这可以通过调用`copy()`方法实现。

```python
x2_sub_copy = x2[:2, :2].copy()
print(x2_sub_copy)
```

输出为：

```
[[99  5]
 [ 7  6]]
```

现在修改得到的子数组，原数组不变：

```python
x2_sub_copy[0, 0] = 42
print(x2_sub_copy)
```

输出为：

```
[[42  5]
 [ 7  6]]
```

查看原始数组：

```
print(x2)
```

结果为：

```
[[99  5  2  4]
 [ 7  6  8  8]
 [ 1  6  7  7]]
```

## 数组变换

另外一个有用的操作是数组变换，通过`reshape()`方法实现。下面的操作将一维数组变换成3 x 3的数组。

```python
grid = np.arange(1, 10).reshape((3, 3))
print(grid)
```

输出为：

```
[[1 2 3]
 [4 5 6]
 [7 8 9]]
```

注意原数组的大小必须和变换后数组的大小一致。如果可能的话，`reshape()`返回的也是原始数组的视图，但如果数组不是连续存储的话，就可能不是视图了。

另外一种常见的变换是将一个一维数组变换为二维行矩阵或者列矩阵，这也可以通过`reshape()`实现。或者通过切片操作时指定`newaxis`参数。

```python
x = np.array([1, 2, 3])

# row vector via reshape
x.reshape((1, 3))
```

输出为：

```python
array([[1, 2, 3]])
```

变换为列矢量：

```python
# column vector via reshape
x.reshape((3, 1))
```

结果为：

```
array([[1],
       [2],
       [3]])
```

等价于：

```python
# column vector via newaxis
x[:, np.newaxis]
```

## 合并和切割数组

前面的操作都是针对单个数组的，也可以将多个数组合并为一个，或者将一个数组拆分为多个。

### 合并数组

数组的合并主要通过以下方法实现：

- `np.concatencate`
- `np.stack`
- `np.vstack`
- `np.hstack`

`np.concatencate`接收数组列表或者元组作为参数。

```python
x = np.array([1, 2, 3])
y = np.array([3, 2, 1])
np.concatenate([x, y])
```

结果为：

```
array([1, 2, 3, 3, 2, 1])
```

也可以同时合并多个数组：

```python
z = [99, 99, 99]
print(np.concatenate([x, y, z]))
```

结果为：

```
[ 1  2  3  3  2  1 99 99 99]
```

还可以用于合并多个二维数组：

```python
grid = np.array([[1, 2, 3],
                 [4, 5, 6]])
# concatenate along the first axis
np.concatenate([grid, grid])
```

结果为：

```
array([[1, 2, 3],
       [4, 5, 6],
       [1, 2, 3],
       [4, 5, 6]])
```

沿横轴合并数组：

```python
# concatenate along the second axis (zero-indexed)
np.concatenate([grid, grid], axis=1)
```

结果为：

```
array([[1, 2, 3, 1, 2, 3],
       [4, 5, 6, 4, 5, 6]])
```

对于不同维度的数组，可以通过`np.vstack()`或者`np.hstack()`函数进行合并。

垂直方向合并：

```
x = np.array([1, 2, 3])
grid = np.array([[9, 8, 7],
                 [6, 5, 4]])

# vertically stack the arrays
np.vstack([x, grid])
```

结果为：

```
array([[1, 2, 3],
       [9, 8, 7],
       [6, 5, 4]])
```

水平合并：

```
# horizontally stack the arrays
y = np.array([[99],
              [99]])
np.hstack([grid, y])
```

结果为：

```
array([[ 9,  8,  7, 99],
       [ 6,  5,  4, 99]])
```

同样的，`np.dstack()`会沿第三条轴进行合并：

### 拆分数组

拆分数组通过以下方法实现：`np.split`, `np.hsplit`, `np.vsplit`，每个函数接收一个索引数组作为参数。

```python
x = [1, 2, 3, 99, 99, 3, 2, 1]
x1, x2, x3 = np.split(x, [3, 5])
print(x1, x2, x3)
# [1 2 3] [99 99] [3 2 1]
```
如果指定N个分割点，则会拆分出N+1个子数组，`np.hsplit`和`np.vsplit`类似。
```python
grid = np.arange(16).reshape((4, 4))
grid
```
输出为：
```
array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11],
       [12, 13, 14, 15]])
```

```python
upper, lower = np.vsplit(grid, [2])
print(upper)
print(lower)
```

输出为：

```
[[0 1 2 3]
 [4 5 6 7]]
[[ 8  9 10 11]
 [12 13 14 15]]
```

```python
left, right = np.hsplit(grid, [2])
print(left)
print(right)
```

输出为：

```
[[ 0  1]
 [ 4  5]
 [ 8  9]
 [12 13]]
[[ 2  3]
 [ 6  7]
 [10 11]
 [14 15]]
```

同样的，`np.dsplit`会沿第三条轴拆分数组。

