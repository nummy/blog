`Numpy`中的数学计算可能很快，也可能很慢。提高计算速度的关键是使用矢量化操作，一般都是通过`Numpy`通用函数实现，这一节主要讲述`Numpy`的通用函数。

Python默认的计算方式非常缓慢，这是由于Python是一门动态编译型语言导致的。有两种方式可以提高运算速度，一是将Python代码编译成C；二是使用`Numpy`进行计算。

下面使用Python计算数组中每个元素的倒数：

```python
import numpy as np
np.random.seed(0)

def compute_reciprocals(values):
    output = np.empty(len(values))
    for i in range(len(values)):
        output[i] = 1.0 / values[i]
    return output
        
values = np.random.randint(1, 10, size=5)
compute_reciprocals(values)
```

结果为：

```
array([ 0.16666667,  1.        ,  0.25      ,  0.25      ,  0.125     ])
```

当计算量增大时，运行速度会非常慢：

```python
big_array = np.random.randint(1, 100, size=1000000)
%timeit compute_reciprocals(big_array)
```

结果为：

```
3.01 s ± 81.4 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

从上可以看出，由于计算时每次都需要检查元素的数据类型，导致时间开销大为增加。

## 通用函数简介

`Numpy`提供了很多类型的通用函数，这些函数的操作也称之为矢量操作。每个操作相当于将对数组的循环操作推进`Numpy`的底层接口，而这个接口是已经编译过的，所以速度会快很多。

比较下面两种操作：

```python
print(compute_reciprocals(values))
print(1.0 / values)
```

查看大数组的计算时间：

```python
%timeit (1.0 / big_array)
```

结果为：

```
5.42 ms ± 178 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```

矢量操作都是基于`Numpy`通用函数`ufunc`实现的，它们的主要目的是快速的对数组的每个元素执行重复的操作。通用函数非常灵活，既可以执行标量和数组之间的操作，也可以执行两个数组之间的操作。

```python
np.arange(5) / np.arange(1, 6)
```

结果为：

```
array([ 0.        ,  0.5       ,  0.66666667,  0.75      ,  0.8       ])
```

通用函数不仅仅可以作用于一维数组，也可以作用与多维数组。

```python
x = np.arange(9).reshape((3, 3))
2 ** x
```

结果为：

```
array([[  1,   2,   4],
       [  8,  16,  32],
       [ 64, 128, 256]])
```

## 探索通用函数

通用函数分为以下两种：

- 一元通用函数
- 二元通用函数

### 算术运算

`Numpy`通用函数使用了Python默认的算术运算符，包括标准的加减乘除等运算。

```python
x = np.arange(4)
print("x     =", x)
print("x + 5 =", x + 5)
print("x - 5 =", x - 5)
print("x * 2 =", x * 2)
print("x / 2 =", x / 2)
print("x // 2 =", x // 2)  # floor division
```

结果为：

```python
x     = [0 1 2 3]
x + 5 = [5 6 7 8]
x - 5 = [-5 -4 -3 -2]
x * 2 = [0 2 4 6]
x / 2 = [ 0.   0.5  1.   1.5]
x // 2 = [0 0 1 1]
```

`Numpy`同样包括了取反，指数`**`，取模`%`等运算的通用函数。

```python
print("-x     = ", -x)
print("x ** 2 = ", x ** 2)
print("x % 2  = ", x % 2)
```

结果为：

```
-x     =  [ 0 -1 -2 -3]
x ** 2 =  [0 1 4 9]
x % 2  =  [0 1 0 1]
```

除此之外，组合运算也是支持的：

```python
-(0.5*x + 1) ** 2
```

结果为：

```
array([-1.  , -2.25, -4.  , -6.25])
```

每个算术运算实际等价于对应的`Numpy`内置函数。例如`+`等价于`add`函数。

```
np.add(x, 2)
```

结果为：

```
array([2, 3, 4, 5])
```

下面的表格列出了`Numpy`实现的算术运算：

| Operator | Equivalent ufunc    | Description                 |
| -------- | ------------------- | --------------------------- |
| ``+``    | ``np.add``          | 加法运算 (例如., ``1 + 1 = 2``)   |
| ``-``    | ``np.subtract``     | 减法运算 (例如., ``3 - 2 = 1``)   |
| ``-``    | ``np.negative``     | 取反运算 (例如., ``-2``)          |
| ``*``    | ``np.multiply``     | 乘法运算(例如., ``2 * 3 = 6``)    |
| ``/``    | ``np.divide``       | 除法运算 (例如., ``3 / 2 = 1.5``) |
| ``//``   | ``np.floor_divide`` | 地板除运算 (例如., ``3 // 2 = 1``) |
| ``**``   | ``np.power``        | 指数运算 (例如., ``2 ** 3 = 8``)  |
| ``%``    | ``np.mod``          | 取模运算(例如., ``9 % 4 = 1``)    |

### 取绝对值

`Numpy`也支持Python内置的`abs()`函数。

```python
x = np.array([-2, -1, 0, 1, 2])
abs(x)
```

结果为：

```
array([2, 1, 0, 1, 2])
```

对应的`Numpy`函数为:

```python
np.absolute(x)
```

结果为：

```
array([2, 1, 0, 1, 2])
```

或者

```
np.abs(x)
```

通用函数还能处理复数：

```python
x = np.array([3 - 4j, 4 - 3j, 2 + 0j, 0 + 1j])
np.abs(x)
```

结果为：

```
array([ 5.,  5.,  2.,  1.])
```

### 三角函数

`Numpy`还提供了三角函数用于数学计算。首先，定义一个数组：

```python
theta = np.linspace(0, np.pi, 3)
```

接下来可以计算它们对应的三角函数值：

```python
print("theta      = ", theta)
print("sin(theta) = ", np.sin(theta))
print("cos(theta) = ", np.cos(theta))
print("tan(theta) = ", np.tan(theta))
```

结果为：

```
theta      =  [ 0.          1.57079633  3.14159265]
sin(theta) =  [  0.00000000e+00   1.00000000e+00   1.22464680e-16]
cos(theta) =  [  1.00000000e+00   6.12323400e-17  -1.00000000e+00]
tan(theta) =  [  0.00000000e+00   1.63312394e+16  -1.22464680e-16]
```

上面得到的值都是基于机器精度的，所以数值并不是特别精确。同样，也可以执行反三角函数运算。

```python
x = [-1, 0, 1]
print("x         = ", x)
print("arcsin(x) = ", np.arcsin(x))
print("arccos(x) = ", np.arccos(x))
print("arctan(x) = ", np.arctan(x))
```

输出结果为：

```
x         =  [-1, 0, 1]
arcsin(x) =  [-1.57079633  0.          1.57079633]
arccos(x) =  [ 3.14159265  1.57079633  0.        ]
arctan(x) =  [-0.78539816  0.          0.78539816]
```

### 指数和对数

`Numpy`通用函数同样支持指数运算和对数运算。

```python
x = [1, 2, 3]
print("x     =", x)
print("e^x   =", np.exp(x))
print("2^x   =", np.exp2(x))
print("3^x   =", np.power(3, x))
```

结果为：

```
x     = [1, 2, 3]
e^x   = [  2.71828183   7.3890561   20.08553692]
2^x   = [ 2.  4.  8.]
3^x   = [ 3  9 27]
```

对数运算同样支持，`np.log`计算自然对数，也可以指定其他基底：

```python
x = [1, 2, 4, 10]
print("x        =", x)
print("ln(x)    =", np.log(x))
print("log2(x)  =", np.log2(x))
print("log10(x) =", np.log10(x))
```

结果为：

```
x        = [1, 2, 4, 10]
ln(x)    = [ 0.          0.69314718  1.38629436  2.30258509]
log2(x)  = [ 0.          1.          2.          3.32192809]
log10(x) = [ 0.          0.30103     0.60205999  1.        ]
```

`Numpy`还提供了特殊的版本用于支持小数值型数组的计算：

```python
x = [0, 0.001, 0.01, 0.1]
print("exp(x) - 1 =", np.expm1(x))
print("log(1 + x) =", np.log1p(x))
```

结果为：

```
exp(x) - 1 = [ 0.          0.0010005   0.01005017  0.10517092]
log(1 + x) = [ 0.          0.0009995   0.00995033  0.09531018]
```

### 特殊的通用函数

`Numpy`提供了很多通用函数，例如双曲三角函数，位运算，比较运算，角度和弧度的互转操作等等，更多函数可以查看`Numpy`文档。

`scipy.special`提供了更为特殊的函数，下面是一个简单示例:

```python
from scipy import special
# Gamma functions (generalized factorials) and related functions
x = [1, 5, 10]
print("gamma(x)     =", special.gamma(x))
print("ln|gamma(x)| =", special.gammaln(x))
print("beta(x, 2)   =", special.beta(x, 2))
# Error function (integral of Gaussian)
# its complement, and its inverse
x = np.array([0, 0.3, 0.7, 1.0])
print("erf(x)  =", special.erf(x))
print("erfc(x) =", special.erfc(x))
print("erfinv(x) =", special.erfinv(x))
```

结果为：

```
gamma(x)     = [  1.00000000e+00   2.40000000e+01   3.62880000e+05]
ln|gamma(x)| = [  0.           3.17805383  12.80182748]
beta(x, 2)   = [ 0.5         0.03333333  0.00909091]
erf(x)  = [ 0.          0.32862676  0.67780119  0.84270079]
erfc(x) = [ 1.          0.67137324  0.32219881  0.15729921]
erfinv(x) = [ 0.          0.27246271  0.73286908         inf]
```

## 通用函数进阶

**指定输出**

对于大规模的运算，有时想指定计算结果的输出源，而不是暂存在一个临时数组中。对于所有的通用函数，都支持一个`out`关键字。

```python
x = np.arange(5)
y = np.empty(5)
np.multiply(x, 10, out=y)
print(y)
```

结果为：

```
[  0.  10.  20.  30.  40.]
```

也可以与数组视图一起配合使用：

```python
y = np.zeros(10)
np.power(2, x, out=y[::2])
print(y)
```

结果为：

```
[  1.   0.   2.   0.   4.   0.   8.   0.  16.   0.]
```

如果直接写成`y[::2] = 2 ** x`，会导致创建一个临时数组保存`2**x`的结果，然后才是将这个数组的结果复制到数组y。如果计算量比较小，区别并不大，但是如果计算量很大的，使用`out`关键字节省的空间可想而知。

### 聚合

对于二元通用函数，可以直接对对象执行有趣的聚合操作，例如利用`reduce`方法对数组求和。

```python
x = np.arange(1, 6)
np.add.reduce(x)
# 15
```

同样的，也可以利用`reduce`计算数组元素的乘积：

```
np.multiply.reduce(x)
# 120
```

如果想要存储计算的中间结果，可以使用`accumulate`方法：

```python
np.add.accumulate(x)
# array([ 1,  3,  6, 10, 15], dtype=int32)
np.multiply.accumulate(x)
# array([  1,   2,   6,  24, 120], dtype=int32)
```

### 外积

通用函数还可以用来计算外积：

```
x = np.arange(1, 6)
np.multiply.outer(x, x)
```

结果为：

```
array([[ 1,  2,  3,  4,  5],
       [ 2,  4,  6,  8, 10],
       [ 3,  6,  9, 12, 15],
       [ 4,  8, 12, 16, 20],
       [ 5, 10, 15, 20, 25]])
```

