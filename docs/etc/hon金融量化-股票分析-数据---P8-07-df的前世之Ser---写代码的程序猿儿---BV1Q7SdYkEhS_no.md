# Python金融量化：P8：07 df的前世之Series

![](img/4be2e1bd23f0e8f018a1260572551a00_1.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解为什么需要Pandas，以及Series的基本概念、创建方法、常用属性和操作。

## 概述：为什么需要Pandas？

![](img/4be2e1bd23f0e8f018a1260572551a00_3.png)

在前面的小节中，我们已经学习了NumPy模块。NumPy是一个基于一维或多维数组的模块，主要用于存储和处理数值型数据。

既然NumPy模块已经可以帮助我们处理数值型数据，为什么还需要学习Pandas呢？原因在于，在数据分析中，除了数值型数据，我们还需要处理大量的非数值型数据，例如字符串类型的数据。Pandas模块就是专门用来存储和处理非数值型数据的工具。

![](img/4be2e1bd23f0e8f018a1260572551a00_5.png)

简单来说，NumPy主要用于处理数值型数据，而Pandas主要用于处理非数值型数据。当然，Pandas也可以处理数值型数据，但其核心优势在于处理非数值型数据。

## Pandas中的两个核心类

![](img/4be2e1bd23f0e8f018a1260572551a00_7.png)

在Pandas模块中，我们需要重点学习两个常用的类：`Series`和`DataFrame`。它们是两种不同形式的数据结构。

![](img/4be2e1bd23f0e8f018a1260572551a00_9.png)

数据结构指的是可以装载数据的容器。就像NumPy是一种数据结构一样，`Series`和`DataFrame`也是Pandas中封装好的数据结构，我们可以将数据存储其中，并利用它们封装好的方法和属性来处理数据。

Pandas主要用于存储和处理非数值型数据。本节课，我们先从`Series`开始学习。

![](img/4be2e1bd23f0e8f018a1260572551a00_11.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_13.png)

## 什么是Series？📊

`Series`是Pandas中的一种数据结构，我们可以暂且将其理解为一个一维数组。它只能是一维的，不能是多维的。

一个`Series`对象主要由两部分组成：
1.  **values**：指的是`Series`中存储的数据元素。
2.  **index**：指的是每个数据元素对应的索引。

![](img/4be2e1bd23f0e8f018a1260572551a00_15.png)

## 创建Series

在创建`Series`之前，我们需要先导入它。

以下是导入`Series`类的代码：
```python
from pandas import Series
```

![](img/4be2e1bd23f0e8f018a1260572551a00_17.png)

导入后，我们就可以使用`Series`类的构造方法来创建对象了。构造方法常用的参数有：
*   `data`：数据源，即要存储的数据。
*   `index`：显示索引，用于指定每个元素的索引标签。
*   `dtype`：数据类型，用于指定存储数据的数据类型。

![](img/4be2e1bd23f0e8f018a1260572551a00_19.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_21.png)

### 使用列表创建Series

我们可以使用列表作为数据源来创建`Series`。

![](img/4be2e1bd23f0e8f018a1260572551a00_23.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
运行上述代码，你会看到类似以下的输出：
```
0    1
1    2
2    3
3    4
dtype: int64
```
输出结果中，第一列是每个元素的索引（默认从0开始，称为隐式索引），第二列是存储的元素值（1, 2, 3, 4）。

![](img/4be2e1bd23f0e8f018a1260572551a00_25.png)

### 使用NumPy数组创建Series

我们也可以使用NumPy数组作为数据源。但需要注意的是，`Series`是一维结构，因此数据源也必须是一维的。

![](img/4be2e1bd23f0e8f018a1260572551a00_27.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_29.png)

```python
import numpy as np
# 创建一维NumPy数组
data_np = np.random.randint(0, 100, size=5)
s = Series(data=data_np)
print(s)
```

![](img/4be2e1bd23f0e8f018a1260572551a00_31.png)

如果尝试使用二维NumPy数组，程序会报错，这再次印证了`Series`是一维数据结构。

![](img/4be2e1bd23f0e8f018a1260572551a00_33.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_35.png)

### 指定显示索引 (index)

![](img/4be2e1bd23f0e8f018a1260572551a00_37.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_39.png)

在创建`Series`时，我们可以通过`index`参数来指定显示索引，这能大大增强数据的可读性。

![](img/4be2e1bd23f0e8f018a1260572551a00_41.png)

```python
s = Series(data=[1, 2, 3, 4], index=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(s)
```
输出结果：
```
A    1
B    2
C    3
D    4
dtype: int64
```
此时，索引不再是0,1,2,3，而是变成了A, B, C, D。显示索引可以让数据含义更直观，例如可以用“语文”、“数学”等作为索引标签。

![](img/4be2e1bd23f0e8f018a1260572551a00_43.png)

### 使用字典创建Series

![](img/4be2e1bd23f0e8f018a1260572551a00_45.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_47.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_49.png)

使用字典创建`Series`非常方便，字典的键（key）会自动成为`Series`的显示索引，字典的值（value）会成为存储的数据。

![](img/4be2e1bd23f0e8f018a1260572551a00_51.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_53.png)

```python
data_dict = {‘语文‘: 100, ‘数学‘: 99, ‘理综‘: 250}
s = Series(data=data_dict)
print(s)
```
输出结果：
```
语文    100
数学     99
理综    250
dtype: int64
```

![](img/4be2e1bd23f0e8f018a1260572551a00_55.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_57.png)

## Series的索引与切片

![](img/4be2e1bd23f0e8f018a1260572551a00_59.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_61.png)

`Series`的索引和切片操作与一维数组非常相似。

![](img/4be2e1bd23f0e8f018a1260572551a00_63.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_65.png)

假设我们有之前用字典创建的`s`：
```python
# 通过位置索引
print(s[0]) # 输出：100
# 通过显示索引 (类似属性访问)
print(s.语文) # 输出：100
# 切片操作
print(s[0:2]) # 输出前两个元素
```

![](img/4be2e1bd23f0e8f018a1260572551a00_67.png)

## Series的常用属性

![](img/4be2e1bd23f0e8f018a1260572551a00_69.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_71.png)

`Series`对象提供了一些常用属性来获取其基本信息。

![](img/4be2e1bd23f0e8f018a1260572551a00_73.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_75.png)

```python
s = Series(data=[10, 20, 30, 40], index=[‘a‘, ‘b‘, ‘c‘, ‘d‘])
print(‘形状:‘, s.shape)    # 输出：(4,)
print(‘元素个数:‘, s.size)   # 输出：4
print(‘索引:‘, s.index)     # 输出：Index([‘a‘, ‘b‘, ‘c‘, ‘d‘], dtype=‘object‘)
print(‘值数组:‘, s.values)  # 输出：[10 20 30 40]
print(‘数据类型:‘, s.dtype)  # 输出：int64
```
需要注意的是，`Series`中存储的数据类型必须是统一的。如果创建时传入不同类型的数据，Pandas会尝试进行统一转换。

![](img/4be2e1bd23f0e8f018a1260572551a00_77.png)

```python
s_mix = Series(data=[1, 2, 3, ‘four‘])
print(s_mix.dtype) # 输出：object (字符串类型)
```

![](img/4be2e1bd23f0e8f018a1260572551a00_79.png)

## Series的常用方法

![](img/4be2e1bd23f0e8f018a1260572551a00_81.png)

`Series`也封装了一些实用的方法。

![](img/4be2e1bd23f0e8f018a1260572551a00_83.png)

```python
import numpy as np
# 创建一个示例Series
data = np.random.randint(60, 100, size=10)
s = Series(data=data)
print(‘原始数据:‘)
print(s)

![](img/4be2e1bd23f0e8f018a1260572551a00_85.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_87.png)

print(‘\n前3个元素:‘)
print(s.head(3)) # 默认显示前5个，head(n)显示前n个

![](img/4be2e1bd23f0e8f018a1260572551a00_89.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_91.png)

print(‘\n后3个元素:‘)
print(s.tail(3)) # 默认显示后5个，tail(n)显示后n个

![](img/4be2e1bd23f0e8f018a1260572551a00_93.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_95.png)

print(‘\n去重后的元素:‘)
print(s.unique()) # 返回去重后的数组

print(‘\n判断每个元素是否为空:‘)
print(s.isnull()) # 为空返回True，否则返回False

![](img/4be2e1bd23f0e8f018a1260572551a00_97.png)

print(‘\n判断每个元素是否非空:‘)
print(s.notnull()) # 非空返回True，否则返回False
```

![](img/4be2e1bd23f0e8f018a1260572551a00_99.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_101.png)

## Series的运算规则

![](img/4be2e1bd23f0e8f018a1260572551a00_103.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_105.png)

两个`Series`进行算术运算时，遵循一个核心规则：**只有索引一致的对应元素才会进行运算，索引不一致的元素在结果中会以空值（NaN）填充**。

![](img/4be2e1bd23f0e8f018a1260572551a00_107.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_109.png)

```python
s1 = Series(data=[1, 2, 3], index=[‘A‘, ‘B‘, ‘C‘])
s2 = Series(data=[1, 2, 3], index=[‘A‘, ‘C‘, ‘D‘])
s_sum = s1 + s2
print(s_sum)
```
输出结果：
```
A    2.0
B    NaN
C    5.0
D    NaN
dtype: float64
```
可以看到，只有索引A和C在两个`Series`中都存在，因此进行了加法运算（1+1=2， 3+2=5）。索引B只在s1中存在，索引D只在s2中存在，因此它们在结果中对应的值都是`NaN`（Not a Number）。

![](img/4be2e1bd23f0e8f018a1260572551a00_111.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_113.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_115.png)

`isnull()`和`notnull()`方法常用来检测运算后产生的这些空值。

![](img/4be2e1bd23f0e8f018a1260572551a00_117.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_119.png)

## 总结

![](img/4be2e1bd23f0e8f018a1260572551a00_121.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_123.png)

本节课我们一起学习了Pandas中的`Series`数据结构。

![](img/4be2e1bd23f0e8f018a1260572551a00_125.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_127.png)

我们首先了解了学习Pandas的目的——处理非数值型数据。然后，我们深入探讨了`Series`，它可以被看作一个带有标签的一维数组，由`values`（值）和`index`（索引）两部分组成。

![](img/4be2e1bd23f0e8f018a1260572551a00_129.png)

![](img/4be2e1bd23f0e8f018a1260572551a00_131.png)

我们学习了三种创建`Series`的方法：使用列表、NumPy数组和字典，并掌握了如何通过`index`参数指定有意义的显示索引。接着，我们熟悉了`Series`的索引、切片操作以及`shape`、`size`、`values`等常用属性。最后，我们介绍了`head()`、`tail()`、`unique()`、`isnull()`等常用方法，并理解了`Series`运算时“索引对齐”的核心规则。

![](img/4be2e1bd23f0e8f018a1260572551a00_133.png)

`Series`是构成更强大的`DataFrame`结构的基础。在下一节课中，我们将学习`DataFrame`，它正是由多个`Series`组合而成的二维表格型数据结构。