# Python金融分析+量化交易：P18：17 df的前世之Series

![](img/eb7031bba2518b5a9954a5c2f220c806_1.png)

## 概述

![](img/eb7031bba2518b5a9954a5c2f220c806_3.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解为什么在NumPy之外还需要Pandas，并掌握Series的创建、索引、切片、常用属性和方法。

## 为什么需要Pandas？

![](img/eb7031bba2518b5a9954a5c2f220c806_5.png)

上一节我们介绍了NumPy模块，它是一个用于处理数值型数据的强大工具。那么，学习Pandas的目的是什么呢？

在我们的数据分析工作中，除了数值型数据，还有大量非数值型数据（如字符串）需要处理。NumPy主要擅长处理数值型数据，而Pandas模块则被设计用来高效地存储和处理非数值型数据。因此，Pandas是对NumPy功能的重要补充。

![](img/eb7031bba2518b5a9954a5c2f220c806_7.png)

## Pandas中的两个核心类

![](img/eb7031bba2518b5a9954a5c2f220c806_9.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_11.png)

Pandas模块主要围绕两个核心类（数据结构）展开：`Series`和`DataFrame`。数据结构可以理解为装载数据的容器。本节中我们先来看看`Series`。

![](img/eb7031bba2518b5a9954a5c2f220c806_13.png)

`Series`是`DataFrame`的组成元素，理解`Series`是学习更复杂的`DataFrame`的基础。

## 什么是Series？

![](img/eb7031bba2518b5a9954a5c2f220c806_15.png)

我们可以将`Series`暂且理解为一个一维数组。它由两部分组成：
*   **values**：存储的数据元素。
*   **index**：每个数据元素对应的索引。

## 创建Series

首先，我们需要导入`Series`类。

![](img/eb7031bba2518b5a9954a5c2f220c806_17.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_19.png)

```python
from pandas import Series
```

![](img/eb7031bba2518b5a9954a5c2f220c806_21.png)

导入后，就可以使用`Series`的构造方法来创建对象了。构造方法有几个常用参数：
*   `data`：数据源。
*   `index`：显式索引。
*   `dtype`：数据类型。

![](img/eb7031bba2518b5a9954a5c2f220c806_23.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_25.png)

以下是几种创建Series的方式：

**1. 使用列表作为数据源**

![](img/eb7031bba2518b5a9954a5c2f220c806_27.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_29.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，第一列是默认的隐式索引（0, 1, 2, 3），第二列是存储的值。

![](img/eb7031bba2518b5a9954a5c2f220c806_31.png)

**2. 使用NumPy数组作为数据源**
需要注意的是，`Series`是一维结构，因此数据源也应当是一维的。

![](img/eb7031bba2518b5a9954a5c2f220c806_33.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_35.png)

```python
import numpy as np
data = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data)
print(s)
```

![](img/eb7031bba2518b5a9954a5c2f220c806_37.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_39.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_41.png)

**3. 使用字典作为数据源**
使用字典时，字典的键（key）会成为Series的显式索引，值（value）成为存储的数据。

```python
data_dict = {'语文': 100, '数学': 99, '理综': 250}
s = Series(data=data_dict)
print(s)
```

![](img/eb7031bba2518b5a9954a5c2f220c806_43.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_45.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_47.png)

**4. 指定显式索引**
我们可以通过`index`参数自定义索引，这能大大增强数据的可读性。

![](img/eb7031bba2518b5a9954a5c2f220c806_49.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_51.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_53.png)

```python
s = Series(data=[1, 2, 3, 4], index=['A', 'B', 'C', 'D'])
print(s)
```

![](img/eb7031bba2518b5a9954a5c2f220c806_55.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_57.png)

## Series的索引与切片

![](img/eb7031bba2518b5a9954a5c2f220c806_59.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_61.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_63.png)

因为`Series`类似于一维数组，所以其索引和切片操作与一维数组非常相似。

![](img/eb7031bba2518b5a9954a5c2f220c806_65.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_67.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
# 索引
print(s[0]) # 通过隐式索引访问
print(s['语文']) # 通过显式索引访问
print(s.语文) # 通过点操作符访问显式索引（索引名需是有效的变量名）

![](img/eb7031bba2518b5a9954a5c2f220c806_69.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_71.png)

# 切片
print(s[0:2]) # 切片操作
```

![](img/eb7031bba2518b5a9954a5c2f220c806_73.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_75.png)

## Series的常用属性

![](img/eb7031bba2518b5a9954a5c2f220c806_77.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_79.png)

`Series`对象提供了一些有用的属性来查看其信息。

![](img/eb7031bba2518b5a9954a5c2f220c806_81.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])

![](img/eb7031bba2518b5a9954a5c2f220c806_83.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_85.png)

print(s.shape)    # 返回形状，对于Series是 (n,)
print(s.size)     # 返回元素个数
print(s.index)    # 返回索引对象
print(s.values)   # 返回存储的数值数组
print(s.dtype)    # 返回元素的数据类型
```
需要注意的是，`Series`中存储的数据类型必须是统一的。如果创建时混用类型（如整数和字符串），Pandas会尝试进行统一转换。

![](img/eb7031bba2518b5a9954a5c2f220c806_87.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_89.png)

## Series的常用方法

![](img/eb7031bba2518b5a9954a5c2f220c806_91.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_93.png)

`Series`也封装了一些实用的方法。

![](img/eb7031bba2518b5a9954a5c2f220c806_95.png)

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))

![](img/eb7031bba2518b5a9954a5c2f220c806_97.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_99.png)

print(s.head(3))  # 显示前3个元素
print(s.tail(3))  # 显示后3个元素
print(s.unique()) # 返回去重后的元素
```

![](img/eb7031bba2518b5a9954a5c2f220c806_101.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_103.png)

**处理空值**
在数据运算中可能会产生空值（NaN），`Series`提供了方法进行判断。

![](img/eb7031bba2518b5a9954a5c2f220c806_105.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_107.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_109.png)

```python
s1 = Series(data=[1, 2, 3], index=['A', 'B', 'C'])
s2 = Series(data=[1, 2, 3], index=['A', 'C', 'D'])
s = s1 + s2 # 索引一致的对应相加，索引不一致的产生NaN
print(s)

![](img/eb7031bba2518b5a9954a5c2f220c806_111.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_113.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_115.png)

print(s.isnull())   # 判断每个元素是否为空，返回布尔Series
print(s.notnull())  # 判断每个元素是否非空，返回布尔Series
```

![](img/eb7031bba2518b5a9954a5c2f220c806_117.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_119.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_121.png)

**算术运算**
`Series`支持直接的算术运算符（`+`, `-`, `*`, `/`），其运算法则是：**只有索引一致的元素才会进行运算，索引不一致的结果为NaN**。同时也对应有方法（`add`, `sub`, `mul`, `div`）。

![](img/eb7031bba2518b5a9954a5c2f220c806_123.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_125.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_127.png)

## 总结

![](img/eb7031bba2518b5a9954a5c2f220c806_129.png)

![](img/eb7031bba2518b5a9954a5c2f220c806_131.png)

本节课中我们一起学习了Pandas的`Series`数据结构。
*   我们了解了`Series`是一个一维的、带索引的数据容器，常用于存储非数值型数据，但也能存储数值型数据。
*   我们掌握了使用列表、NumPy数组和字典来创建`Series`，并学会了如何指定显式索引以增强可读性。
*   我们熟悉了`Series`的索引、切片操作，以及`shape`、`size`、`values`等常用属性。
*   我们学习了`head`、`tail`、`unique`、`isnull`等常用方法，并理解了`Series`在进行算术运算时“索引对齐”的核心规则。

![](img/eb7031bba2518b5a9954a5c2f220c806_133.png)

`Series`是构成更强大的`DataFrame`的基础，下一节我们将进入`DataFrame`的学习。