# 数据分析之量化案例：P7：day02-01 df的前世之Series~1 📊

![](img/3fb59a693134e3c90a6e68bebfcdf849_1.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解为什么需要Pandas，以及Series如何作为处理非数值型数据的基础容器。

## 概述：为什么需要Pandas？ 🤔

![](img/3fb59a693134e3c90a6e68bebfcdf849_3.png)

上一节我们介绍了NumPy模块，它是一个用于存储和处理数值型数据的强大工具。然而，在数据分析中，我们经常需要处理大量的非数值型数据，例如字符串。NumPy并不擅长处理这类数据。

因此，我们需要学习Pandas模块。Pandas主要用于存储和处理**非数值型数据**。当然，它也可以处理数值型数据，但其核心优势在于对非数值型数据的操作。

![](img/3fb59a693134e3c90a6e68bebfcdf849_5.png)

## Pandas中的两个核心类 📚

![](img/3fb59a693134e3c90a6e68bebfcdf849_7.png)

在Pandas模块中，我们将重点学习两个常用的类，它们也是两种不同的数据结构：
*   **Series**
*   **DataFrame**

![](img/3fb59a693134e3c90a6e68bebfcdf849_9.png)

数据结构可以理解为一种容器，用于装载数据。Series和DataFrame就是Pandas中用于装载数据的容器，并且它们提供了丰富的方法来处理其中存储的数据。

![](img/3fb59a693134e3c90a6e68bebfcdf849_11.png)

本节中，我们先来学习Series，它是理解更复杂的DataFrame的基础。

![](img/3fb59a693134e3c90a6e68bebfcdf849_13.png)

## 什么是Series？ 🧱

Series是一种一维的数据结构，可以暂且将其理解为一个**一维数组**。它由两部分组成：
1.  **values**：存储的数据元素。
2.  **index**：每个数据元素对应的索引。

![](img/3fb59a693134e3c90a6e68bebfcdf849_15.png)

## 创建Series 🛠️

首先，我们需要导入Series类。

```python
from pandas import Series
```

![](img/3fb59a693134e3c90a6e68bebfcdf849_17.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_19.png)

导入后，就可以使用Series的构造方法来创建对象了。构造方法常用的参数有：
*   `data`：数据源。
*   `index`：显示索引（可选）。
*   `dtype`：数据类型（可选）。

![](img/3fb59a693134e3c90a6e68bebfcdf849_21.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_23.png)

以下是创建Series的几种常用方式。

![](img/3fb59a693134e3c90a6e68bebfcdf849_25.png)

### 使用列表创建

![](img/3fb59a693134e3c90a6e68bebfcdf849_27.png)

列表可以作为`data`参数传入。

![](img/3fb59a693134e3c90a6e68bebfcdf849_29.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，第一列是索引（默认从0开始，称为隐式索引），第二列是存储的值。

![](img/3fb59a693134e3c90a6e68bebfcdf849_31.png)

### 使用NumPy数组创建

![](img/3fb59a693134e3c90a6e68bebfcdf849_33.png)

一维的NumPy数组也可以作为数据源。注意，Series是**一维**结构，因此数据源必须是一维的。

![](img/3fb59a693134e3c90a6e68bebfcdf849_35.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_37.png)

```python
import numpy as np
data_np = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data_np)
print(s)
```

![](img/3fb59a693134e3c90a6e68bebfcdf849_39.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_41.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_43.png)

### 使用字典创建

![](img/3fb59a693134e3c90a6e68bebfcdf849_45.png)

使用字典创建Series时，字典的**键（key）** 会成为Series的显示索引，字典的**值（value）** 会成为存储的数据。

![](img/3fb59a693134e3c90a6e68bebfcdf849_47.png)

```python
data_dict = {'语文': 100, '数学': 99, '理综': 250}
s = Series(data=data_dict)
print(s)
```

![](img/3fb59a693134e3c90a6e68bebfcdf849_49.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_51.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_53.png)

### 指定显示索引

![](img/3fb59a693134e3c90a6e68bebfcdf849_55.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_57.png)

我们可以通过`index`参数来指定显示索引，这能大大增强数据的可读性。

![](img/3fb59a693134e3c90a6e68bebfcdf849_59.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_61.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
print(s)
```
如果没有指定`index`，则使用默认的隐式索引（0, 1, 2...）。指定`index`后，就使用了更具描述性的显示索引。

![](img/3fb59a693134e3c90a6e68bebfcdf849_63.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_65.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_67.png)

## Series的索引与切片 🔍

![](img/3fb59a693134e3c90a6e68bebfcdf849_69.png)

Series的索引和切片操作与一维数组非常相似。

![](img/3fb59a693134e3c90a6e68bebfcdf849_71.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_73.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_75.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
# 索引
print(s[0]) # 通过位置索引访问
print(s['语文']) # 通过显示索引访问
print(s.语文) # 通过属性方式访问（仅当索引是有效的Python标识符时）

![](img/3fb59a693134e3c90a6e68bebfcdf849_77.png)

# 切片
print(s[0:2]) # 切片操作
```

![](img/3fb59a693134e3c90a6e68bebfcdf849_79.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_81.png)

## Series的常用属性 📊

![](img/3fb59a693134e3c90a6e68bebfcdf849_83.png)

Series对象提供了一些有用的属性来查看其信息。

![](img/3fb59a693134e3c90a6e68bebfcdf849_85.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_87.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])

![](img/3fb59a693134e3c90a6e68bebfcdf849_89.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_91.png)

print(s.shape)    # 返回形状，对于Series是 (n,)
print(s.size)     # 返回元素个数
print(s.index)    # 返回索引对象
print(s.values)   # 返回存储值的数组
print(s.dtype)    # 返回元素的数据类型
```
**注意**：Series中存储的数据类型必须是统一的。如果创建时传入混合类型（如整数和字符串），Pandas会尝试将其统一为一种类型（通常是`object`，即字符串类型）。

![](img/3fb59a693134e3c90a6e68bebfcdf849_93.png)

## Series的常用方法 ⚙️

![](img/3fb59a693134e3c90a6e68bebfcdf849_95.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_97.png)

以下是Series的一些常用方法。

![](img/3fb59a693134e3c90a6e68bebfcdf849_99.png)

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))

![](img/3fb59a693134e3c90a6e68bebfcdf849_101.png)

print(s.head(3))   # 显示前3个元素
print(s.tail(3))   # 显示后3个元素
print(s.unique())  # 返回去重后的元素
print(s.isnull())  # 判断每个元素是否为空（NaN），返回布尔Series
print(s.notnull()) # 判断每个元素是否非空，返回布尔Series
```

![](img/3fb59a693134e3c90a6e68bebfcdf849_103.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_105.png)

### Series的运算规则

![](img/3fb59a693134e3c90a6e68bebfcdf849_107.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_109.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_111.png)

当两个Series进行算术运算（如相加）时，遵循一个核心规则：**只有索引相同的元素才会进行运算**。索引不同的元素，结果会用`NaN`（Not a Number，空值）填充。

![](img/3fb59a693134e3c90a6e68bebfcdf849_113.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_115.png)

```python
s1 = Series(data=[1, 2, 3], index=['A', 'B', 'C'])
s2 = Series(data=[1, 2, 3], index=['A', 'C', 'D'])

![](img/3fb59a693134e3c90a6e68bebfcdf849_117.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_119.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_121.png)

s_sum = s1 + s2
print(s_sum)
```
输出结果中，只有索引`A`和`C`对应的值成功相加（1+1=2， 3+3=6）。索引`B`和`D`在对方Series中没有匹配的索引，因此结果为`NaN`。`isnull()`和`notnull()`方法就是用来检测这些空值的。

![](img/3fb59a693134e3c90a6e68bebfcdf849_123.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_125.png)

此外，Series也支持直接的算术运算符（`+`, `-`, `*`, `/`）或对应的方法（`add()`, `sub()`, `mul()`, `div()`）进行元素级运算。

![](img/3fb59a693134e3c90a6e68bebfcdf849_127.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_129.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_131.png)

## 总结 📝

![](img/3fb59a693134e3c90a6e68bebfcdf849_133.png)

![](img/3fb59a693134e3c90a6e68bebfcdf849_135.png)

本节课中我们一起学习了Pandas中的Series数据结构。
*   Series是一个**一维**的数据容器，由**值（values）**和**索引（index）** 组成。
*   我们可以使用**列表、NumPy数组、字典**来创建Series，并可以通过`index`参数指定有意义的**显示索引**以提高可读性。
*   Series的**索引、切片、属性**（如`shape`, `values`）和**方法**（如`head()`, `unique()`）操作简单直观。
*   Series进行算术运算时，遵循**索引对齐**原则，只有索引匹配的元素才会参与计算。

![](img/3fb59a693134e3c90a6e68bebfcdf849_137.png)

Series是构成更强大的DataFrame的基石。在下一节中，我们将看到如何由多个Series组成一个二维的DataFrame，从而处理更复杂的表格型数据。