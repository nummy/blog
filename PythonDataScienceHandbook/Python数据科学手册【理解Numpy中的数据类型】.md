## 理解Python中的数据类型

高效的数据驱动科学计算需要深入理解数据是如何存储和处理的。这一节我们将对比数组数据在Python和Numpy中是如何处理的。理解两者之间的区别是理解整本书后面知识的基石。

通常我们使用Python是因为它的简单易用，其中很重要的一点就是动态类型，不像C++或者Java，它们需要在声明的时候指定数据类型，而像Python这样的动态语言不需要声明数据类型。例如，在C语言中，你可能这样写:

```c
/* C code */
int result = 0;
for(int i=0; i<100; i++){
    result += i;
}
```

而等价的Python操作如下：

```python
# Python code
result = 0
for i in range(100):
    result += i
```

注意两者之间的区别：在C中，每个变量的数据类型都是显式声明的，而在Python中数据类型是动态推断的，也就是说，在Python中，我们可以给同一个变量赋予不同类型的值。

```python
# Python code
x = 4
x = "four"
```

上面我们将x值得类型从整型改为字符串，如果同样的事情发生在C中，结果会导致编译错误：

```c
/* C code */
int x = 4;
x = "four";  // FAILS
```

这种灵活性使Python等动态语言更为容易上手，理解这一点可以让我们可以让我们学会更加高效的使用Python进行数据分析。但是这种灵活性也说明Python变量不仅仅是指它们的值，它们还包含了其他关于数据类型的信息。

## Python 整型

标准Python是基于C实现的，也就是说每个Python对象都是一个C结构体，这个结构体不仅仅包含了值得信息，还包括了其它很多信息。例如，当我我们定义整数`x=1000`时，x不仅仅是一个简单的整型数据，实际上它是一个指向C结构体的指针，这个指针包含了几个值。翻看Python3.4的源码实现，可以发现整型的定义如下所示：

```C
struct _longobject {
    long ob_refcnt;
    PyTypeObject *ob_type;
    size_t ob_size;
    long ob_digit[1];
};
```

Python3.4中整型实际包含了以下几个部分：

- `ob_refcnt` 引用计数，帮助Python处理内存分配和释放
- `ob_type` 指明数据类型
- `ob_size` 指明接下来数据成员的个数
- `ob_digit` 实际存储的整型值

下面这幅图说明了整型数据在C和Python中存储结构：

![Integer Memory Layout](./img/cint_vs_pyint.png)

这里的`PyObject_HEAD`指的是上面提到的结构体的部分信息，它包含了引用计数，数据类型等其他信息。

注意这里的区别：在C中，整型数据就是一个整数数据，但是在Python中，整型数据是一个指针，指向一个对象，格外的信息使Python更容易编码和动态赋值。这些格外的信息使Python花费了更多的空间。

## Python列表

接下来分析下Python列表的实现方式，首先创建几个列表：

```
L = list(range(10))
# [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
L2 = [str(c) for c in L]
# ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
L3 = [True, "2", 3.0, 4]
```

Python列表中元素的数据类型可以是多种多样的，非常灵活，但是这种灵活的代价是每个元素都必须包含数据类型，引用计数等其他信息，每个元素实际都是一个Python对象。如果列表中的元素数据类型一致，则很多信息都是冗余的，如果使用同质的数组存储的话更为高效。Python列表和Numpy列表的区别如下图所示：

![](./img/array_vs_list.png)

从实现上来讲，Numpy数组包含了一个指针，指向一块连续存储的数据。而Python列表，也包含了一个指针，但是指向一块连续存储的指针，这些指针又指向一个结构体，这个结构体包含了数据信息。

### Python 数组

Python内置的`array` 可以创建相同数据类型的数组。

```python
import array
L = list(range(10))
A = array.array('i', L)
# array('i', [0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

这里`i` 指明了数组的数据类型为整型。

但是使用Numpy的数组更为高效，首先导入Numpy

```python
import numpy as np
```

## 从Python列表中创建数组

可以使用`np.array` 从列表中创建数组：

```python
# integer array:
np.array([1, 4, 2, 5, 3])
# array([1, 4, 2, 5, 3])
```

注意更Python列表不同，Numpy数组中的元素必须是同一数据类型，如果数据类型不一致，Numpy会向上转型(如果可以的话)

```python
np.array([3.14, 4, 2, 3])
# array([ 3.14,  4.  ,  2.  ,  3.  ])
```

如果需要显式声明结果数组中元素的类型，可以通过`dtype`来指定：

```python
np.array([1, 2, 3, 4], dtype='float32')
# array([ 1.,  2.,  3.,  4.], dtype=float32)
```

Numpy还可以声明多维数组：

```python
# nested lists result in multi-dimensional arrays
np.array([range(i, i + 3) for i in [2, 4, 6]])
```

结果为：

```
array([[2, 3, 4],
       [4, 5, 6],
       [6, 7, 8]])
```

### 其它创建数组的方式

Numpy提供了一些函数用于创建特殊的数组。

创建值为0的数组。

```
# Create a length-10 integer array filled with zeros
np.zeros(10, dtype=int)
```

结果为：

```
array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])
```

创建值全部为1的数组：

```python
# Create a 3x5 floating-point array filled with ones
np.ones((3, 5), dtype=float)
```

结果为：

```
array([[ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.]])
```

指定填充值：

```
# Create a 3x5 array filled with 3.14
np.full((3, 5), 3.14)
```

结果为：

```
array([[ 3.14,  3.14,  3.14,  3.14,  3.14],
       [ 3.14,  3.14,  3.14,  3.14,  3.14],
       [ 3.14,  3.14,  3.14,  3.14,  3.14]])
```

创建指定序列的数组：

```python
# Create an array filled with a linear sequence
# Starting at 0, ending at 20, stepping by 2
# (this is similar to the built-in range() function)
np.arange(0, 20, 2)
```

结果为：

```
array([ 0,  2,  4,  6,  8, 10, 12, 14, 16, 18])
```

创建均匀分布的数组：

```
# Create an array of five values evenly spaced between 0 and 1
np.linspace(0, 1, 5)
```

结果为：

```
array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ])
```

创建均匀分布的随机数组：

```
# Create a 3x3 array of uniformly distributed
# random values between 0 and 1
np.random.random((3, 3))
```

结果为：

```
array([[ 0.99844933,  0.52183819,  0.22421193],
       [ 0.08007488,  0.45429293,  0.20941444],
       [ 0.14360941,  0.96910973,  0.946117  ]])
```

创建正态分布的随机数组：

```
# Create a 3x3 array of normally distributed random values
# with mean 0 and standard deviation 1
np.random.normal(0, 1, (3, 3))
```

结果为：

```
array([[ 1.51772646,  0.39614948, -0.10634696],
       [ 0.25671348,  0.00732722,  0.37783601],
       [ 0.68446945,  0.15926039, -0.70744073]])
```

创建内容为随机整数的数组：

```python
# Create a 3x3 array of random integers in the interval [0, 10)
np.random.randint(0, 10, (3, 3))
```

结果为：

```
array([[2, 3, 4],
       [5, 7, 8],
       [0, 5, 0]])
```

创建单位矩阵：

```python
# Create a 3x3 identity matrix
np.eye(3)
```

结果为：

```
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
```

创建空数组：

```python
# Create an uninitialized array of three integers
# The values will be whatever happens to already exist at that memory location
np.empty(3)
```

结果为：

```
array([ 1.,  1.,  1.])
```

## Numpy标准数据类型

由于Numpy是用C编写的，所以它提供的数据类型与C的类似。标准Numpy数据类型如下表所示：

| Data type      | Description                              |
| -------------- | ---------------------------------------- |
| ``bool_``      | Boolean (True or False) stored as a byte |
| ``int_``       | Default integer type (same as C ``long``; normally either ``int64`` or ``int32``) |
| ``intc``       | Identical to C ``int`` (normally ``int32`` or ``int64``) |
| ``intp``       | Integer used for indexing (same as C ``ssize_t``; normally either ``int32`` or ``int64``) |
| ``int8``       | Byte (-128 to 127)                       |
| ``int16``      | Integer (-32768 to 32767)                |
| ``int32``      | Integer (-2147483648 to 2147483647)      |
| ``int64``      | Integer (-9223372036854775808 to 9223372036854775807) |
| ``uint8``      | Unsigned integer (0 to 255)              |
| ``uint16``     | Unsigned integer (0 to 65535)            |
| ``uint32``     | Unsigned integer (0 to 4294967295)       |
| ``uint64``     | Unsigned integer (0 to 18446744073709551615) |
| ``float_``     | Shorthand for ``float64``.               |
| ``float16``    | Half precision float: sign bit, 5 bits exponent, 10 bits mantissa |
| ``float32``    | Single precision float: sign bit, 8 bits exponent, 23 bits mantissa |
| ``float64``    | Double precision float: sign bit, 11 bits exponent, 52 bits mantissa |
| ``complex_``   | Shorthand for ``complex128``.            |
| ``complex64``  | Complex number, represented by two 32-bit floats |
| ``complex128`` | Complex number, represented by two 64-bit floats |

在使用中既可以通过字符串指定：

```python
np.zeros(10, dtype='int16')
```

也可以通过相关的Numpy对象指定：

```
np.zeros(10, dtype=np.int16)
```

