# Python数据分析：P8：02 pandas修真之前戏基础_01 df的前世之Series 📊

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_1.png)

## 概述
在本节课中，我们将要学习pandas模块中的第一个核心数据结构——Series。我们将了解它是什么、为什么需要它、如何创建它以及如何使用它的一些基本属性和方法。理解Series是后续学习更强大的DataFrame的基础。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_3.png)

---

## 为什么需要pandas？🤔
上一节我们回顾了NumPy模块，它主要用于存储和处理数值型数据。但在数据分析中，我们经常需要处理大量的非数值型数据，例如字符串。pandas模块就是为了高效处理这类非数值型数据而设计的。虽然pandas也能处理数值型数据，但其核心优势在于处理非数值型数据。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_5.png)

---

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_7.png)

## pandas中的两个核心类
在pandas模块中，我们需要重点学习两个类：**Series**和**DataFrame**。它们是两种不同的数据结构，可以理解为装载数据的容器。我们将数据存储在这些容器中，然后利用它们封装好的工具和方法进行处理。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_9.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_11.png)

本节我们先来学习Series。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_13.png)

---

## 什么是Series？📈
Series是一种可以装载数据的一维数据结构。你可以暂且把它理解成一个一维数组。它由两部分组成：
*   **values**：存储的数据元素。
*   **index**：每个数据元素对应的索引。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_15.png)

---

## 创建Series 🛠️
首先，我们需要导入Series类。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_17.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_19.png)

```python
from pandas import Series
```

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_21.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_23.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_25.png)

导入后，就可以使用Series的构造方法来创建它了。构造方法的主要参数有：
*   `data`：数据源。
*   `index`：索引（可选）。
*   `dtype`：数据类型（可选）。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_27.png)

以下是几种创建Series的方式：

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_29.png)

### 1. 使用列表作为数据源
我们可以直接传入一个列表。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_31.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_33.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，第一列是索引（默认从0开始），第二列是数据值。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_35.png)

### 2. 使用NumPy数组作为数据源
Series是一维的，因此数据源也必须是一维的。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_37.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_39.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_41.png)

```python
import numpy as np
arr = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=arr)
print(s)
```

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_43.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_45.png)

### 3. 使用字典作为数据源
使用字典时，字典的键（key）会自动成为Series的显示索引，值（value）成为数据。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_47.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_49.png)

```python
dic = {‘语文‘: 100, ‘数学‘: 99, ‘理综‘: 250}
s = Series(data=dic)
print(s)
```

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_51.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_53.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_55.png)

### 4. 指定显示索引
我们可以通过`index`参数自定义索引，这能大大增强数据的可读性。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_57.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_59.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_61.png)

```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
print(s)
```

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_63.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_65.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_67.png)

---

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_69.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_71.png)

## Series的索引与切片 🔍
Series的索引和切片操作与一维数组非常相似。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_73.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_75.png)

*   **按位置索引**：`s[0]`
*   **按显示索引索引**：`s[‘语文‘]` 或 `s.语文`
*   **切片**：`s[0:2]`

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_77.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_79.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_81.png)

```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
print(s[0]) # 输出：100
print(s[‘语文‘]) # 输出：100
print(s.语文) # 输出：100
print(s[0:2]) # 输出前两个元素
```

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_83.png)

---

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_85.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_87.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_89.png)

## Series的常用属性 📋
Series对象有一些常用的属性，可以快速获取其信息。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_91.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_93.png)

*   `s.shape`：返回Series的形状（元组形式）。
*   `s.size`：返回Series中元素的个数。
*   `s.index`：返回Series的索引。
*   `s.values`：返回Series的数据值（一个数组）。
*   `s.dtype`：返回Series中元素的数据类型。**需要注意的是，Series中存储的数据类型必须是统一的**。如果创建时混用类型（如整数和字符串），pandas会尝试进行统一转换。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_95.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_97.png)

```python
s = Series(data=[1, 2, 3, ‘four‘])
print(s.dtype) # 输出：object (字符串类型)
```

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_99.png)

---

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_101.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_103.png)

## Series的常用方法 ⚙️
Series也提供了一些实用的方法。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_105.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_107.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_109.png)

*   `s.head(n)`：显示前n个数据，默认为5。
*   `s.tail(n)`：显示后n个数据，默认为5。
*   `s.unique()`：返回去重后的数据。
*   `s.isnull()`：判断每个元素是否为空（NaN），返回布尔值Series。
*   `s.notnull()`：判断每个元素是否非空，返回布尔值Series。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_111.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_113.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_115.png)

### Series的运算规则
当两个Series进行算术运算（如相加）时，**遵循索引对齐原则**：只有索引相同的元素才会进行运算，索引不同的位置结果会填充为`NaN`（空值）。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_117.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_119.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_121.png)

```python
s1 = Series(data=[1, 2, 3], index=[‘A‘, ‘B‘, ‘C‘])
s2 = Series(data=[1, 2, 3], index=[‘A‘, ‘D‘, ‘C‘])
s = s1 + s2
print(s)
# 输出：
# A    2.0
# B    NaN
# C    6.0
# D    NaN
# dtype: float64
```
此时，`s.isnull()`和`s.notnull()`方法就派上用场了，可以用来检测哪些位置是空值。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_123.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_125.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_127.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_129.png)

---

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_131.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_133.png)

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_135.png)

## 总结 🎯
本节课我们一起学习了pandas中的Series数据结构。
*   Series是一个**一维**的数据结构，由**values（值）**和**index（索引）** 组成。
*   我们可以使用**列表、NumPy数组、字典**来创建Series，并能通过`index`参数指定有意义的**显示索引**以提高可读性。
*   它的**索引、切片、属性（shape, size, index, values, dtype）和方法（head, tail, unique, isnull）** 都非常易于使用。
*   特别需要注意的是，Series进行算术运算时遵循**索引对齐**规则，索引不匹配的位置会产生`NaN`值。

![](img/4b22a1e22a7a5e9d9efb36b72d630a60_137.png)

理解Series是至关重要的，因为它是构成更强大的二维数据结构DataFrame的基本单元。在下一节中，我们将基于对Series的认识，正式学习DataFrame。