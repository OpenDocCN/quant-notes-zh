# 数据分析+金融量化+数据清洗：P8：01 df的前世之Series 📊

![](img/034363744f237fad929a25e809a2d0e9_1.png)

## 概述
在本节课中，我们将开始学习Pandas模块。我们将了解为什么在学习了NumPy之后还需要Pandas，并重点介绍Pandas中的第一个核心数据结构——Series。我们将学习如何创建Series，以及它的基本属性、索引、切片和常用方法。

![](img/034363744f237fad929a25e809a2d0e9_3.png)

## 为什么需要Pandas？🤔
上一节我们回顾了NumPy模块，它是一个用于存储和处理**数值型数据**的数组容器。那么，为什么还需要学习Pandas呢？

在数据分析中，除了数值型数据，我们还需要处理大量的**非数值型数据**，例如字符串。Pandas模块就是专门设计用来高效存储和处理**非数值型数据**的。当然，它也可以处理数值型数据，但其核心优势在于处理混合类型的数据。

![](img/034363744f237fad929a25e809a2d0e9_5.png)

## Pandas的核心数据结构
在Pandas模块中，我们将重点学习两个常用的类，它们也是两种核心的数据结构：
1.  **Series**
2.  **DataFrame**

![](img/034363744f237fad929a25e809a2d0e9_7.png)

数据结构可以理解为一种“容器”，用于装载数据。NumPy的数组是一种数据结构，Pandas的Series和DataFrame也是。我们可以将数据存储在其中，并利用它们封装好的方法和属性进行处理。

![](img/034363744f237fad929a25e809a2d0e9_9.png)

![](img/034363744f237fad929a25e809a2d0e9_11.png)

接下来，我们先从Series开始学习。

![](img/034363744f237fad929a25e809a2d0e9_13.png)

## 什么是Series？📈
Series是Pandas中的一种数据结构。我们可以暂且将其理解为一个**一维数组**。它由两部分组成：
*   **values**：存储的数据元素。
*   **index**：每个数据元素对应的索引。

![](img/034363744f237fad929a25e809a2d0e9_15.png)

其核心概念可以表示为：
**Series = Index + Values**

## 创建Series 🛠️
在创建Series之前，需要先导入它。

```python
from pandas import Series
```

![](img/034363744f237fad929a25e809a2d0e9_17.png)

![](img/034363744f237fad929a25e809a2d0e9_19.png)

![](img/034363744f237fad929a25e809a2d0e9_21.png)

创建Series主要使用其构造方法 `Series()`。以下是几种常见的创建方式：

![](img/034363744f237fad929a25e809a2d0e9_23.png)

![](img/034363744f237fad929a25e809a2d0e9_25.png)

### 1. 使用列表创建
列表可以作为Series的数据源（`data`参数）。

![](img/034363744f237fad929a25e809a2d0e9_27.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
运行结果中，第一列是索引（默认从0开始，称为**隐式索引**），第二列是值（values）。

![](img/034363744f237fad929a25e809a2d0e9_29.png)

### 2. 使用NumPy数组创建
一维的NumPy数组也可以作为数据源。这印证了Series是一维数据结构。

![](img/034363744f237fad929a25e809a2d0e9_31.png)

![](img/034363744f237fad929a25e809a2d0e9_33.png)

```python
import numpy as np
data_np = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data_np)
print(s)
```

![](img/034363744f237fad929a25e809a2d0e9_35.png)

![](img/034363744f237fad929a25e809a2d0e9_37.png)

### 3. 使用字典创建
使用字典创建Series非常方便：字典的**键（key）** 会成为Series的**显示索引**，字典的**值（value）** 会成为Series的**值（values）**。

![](img/034363744f237fad929a25e809a2d0e9_39.png)

![](img/034363744f237fad929a25e809a2d0e9_41.png)

![](img/034363744f237fad929a25e809a2d0e9_43.png)

```python
data_dict = {‘语文‘: 100, ‘数学‘: 99, ‘理综‘: 250}
s = Series(data=data_dict)
print(s)
```

![](img/034363744f237fad929a25e809a2d0e9_45.png)

### 4. 指定显示索引
在创建时，可以通过`index`参数自定义显示索引，这能大大增强数据的可读性。

![](img/034363744f237fad929a25e809a2d0e9_47.png)

![](img/034363744f237fad929a25e809a2d0e9_49.png)

![](img/034363744f237fad929a25e809a2d0e9_51.png)

```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
print(s)
```

![](img/034363744f237fad929a25e809a2d0e9_53.png)

![](img/034363744f237fad929a25e809a2d0e9_55.png)

![](img/034363744f237fad929a25e809a2d0e9_57.png)

**显示索引**相比默认的**隐式索引**（0,1,2...），能更直观地表明每个数据所代表的含义。

![](img/034363744f237fad929a25e809a2d0e9_59.png)

![](img/034363744f237fad929a25e809a2d0e9_61.png)

![](img/034363744f237fad929a25e809a2d0e9_63.png)

## Series的索引与切片 🔍
Series的索引和切片操作与一维数组非常相似。

![](img/034363744f237fad929a25e809a2d0e9_65.png)

![](img/034363744f237fad929a25e809a2d0e9_67.png)

![](img/034363744f237fad929a25e809a2d0e9_69.png)

假设有Series `s`：
```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
```

![](img/034363744f237fad929a25e809a2d0e9_71.png)

![](img/034363744f237fad929a25e809a2d0e9_73.png)

*   **索引**：获取单个值。
    ```python
    print(s[0]) # 通过隐式索引获取：100
    print(s[‘语文‘]) # 通过显示索引获取：100
    print(s.语文) # 通过属性方式获取（显示索引作为属性名）：100
    ```
*   **切片**：获取一部分值。
    ```python
    print(s[0:2]) # 切片，获取前两个元素
    ```

![](img/034363744f237fad929a25e809a2d0e9_75.png)

![](img/034363744f237fad929a25e809a2d0e9_77.png)

![](img/034363744f237fad929a25e809a2d0e9_79.png)

## Series的常用属性 📋
Series对象有一些常用的属性，可以快速获取其信息。

![](img/034363744f237fad929a25e809a2d0e9_81.png)

![](img/034363744f237fad929a25e809a2d0e9_83.png)

以下是几个关键属性：
*   `shape`：返回Series的形状（长度）。
*   `size`：返回Series中元素的个数。
*   `index`：返回Series的索引对象。
*   `values`：返回Series的值（一个数组）。
*   `dtype`：返回Series中元素的数据类型。**Series中存储的数据类型必须是统一的**。

![](img/034363744f237fad929a25e809a2d0e9_85.png)

示例：
```python
s = Series(data=[1, 2, 3, 4])
print(s.shape)   # (4,)
print(s.size)    # 4
print(s.index)   # RangeIndex(start=0, stop=4, step=1)
print(s.values)  # [1 2 3 4]
print(s.dtype)   # int64
```

![](img/034363744f237fad929a25e809a2d0e9_87.png)

![](img/034363744f237fad929a25e809a2d0e9_89.png)

![](img/034363744f237fad929a25e809a2d0e9_91.png)

## Series的常用方法 ⚙️
Series封装了许多实用的方法。以下是几个基础且重要的方法：

![](img/034363744f237fad929a25e809a2d0e9_93.png)

*   `head(n)`：返回前n个元素，默认n=5。
*   `tail(n)`：返回后n个元素，默认n=5。
*   `unique()`：返回去重后的元素数组。
*   `isnull()`：检查每个元素是否为空（NaN），返回布尔值Series。
*   `notnull()`：检查每个元素是否非空，返回布尔值Series。

![](img/034363744f237fad929a25e809a2d0e9_95.png)

![](img/034363744f237fad929a25e809a2d0e9_97.png)

示例：
```python
s = Series(data=np.random.randint(60, 100, size=10))
print(s.head(3)) # 查看前3个元素
print(s.tail(3)) # 查看后3个元素
print(s.unique()) # 查看去重后的值
```

![](img/034363744f237fad929a25e809a2d0e9_99.png)

![](img/034363744f237fad929a25e809a2d0e9_101.png)

## Series的运算规则 ➕➖✖️➗
当两个Series进行算术运算（如加减乘除）时，遵循一个核心规则：**只有索引相同的元素才会进行运算**。

![](img/034363744f237fad929a25e809a2d0e9_103.png)

![](img/034363744f237fad929a25e809a2d0e9_105.png)

如果索引不同，则结果对应位置会填充空值（NaN）。

![](img/034363744f237fad929a25e809a2d0e9_107.png)

![](img/034363744f237fad929a25e809a2d0e9_109.png)

![](img/034363744f237fad929a25e809a2d0e9_111.png)

![](img/034363744f237fad929a25e809a2d0e9_113.png)

```python
s1 = Series(data=[1, 2, 3], index=[‘A‘, ‘B‘, ‘C‘])
s2 = Series(data=[1, 2, 3], index=[‘A‘, ‘C‘, ‘D‘])
s = s1 + s2
print(s)
```
输出结果中，只有索引’A‘和’C‘对应的值相加得到结果（2和6）。索引’B‘和’D‘在对方Series中没有匹配项，因此结果为NaN。

![](img/034363744f237fad929a25e809a2d0e9_115.png)

![](img/034363744f237fad929a25e809a2d0e9_117.png)

![](img/034363744f237fad929a25e809a2d0e9_119.png)

`isnull()`和`notnull()`方法常用来检测此类运算后产生的空值。

![](img/034363744f237fad929a25e809a2d0e9_121.png)

![](img/034363744f237fad929a25e809a2d0e9_123.png)

![](img/034363744f237fad929a25e809a2d0e9_125.png)

![](img/034363744f237fad929a25e809a2d0e9_127.png)

## 总结
本节课我们一起学习了Pandas的核心数据结构之一——**Series**。

![](img/034363744f237fad929a25e809a2d0e9_129.png)

![](img/034363744f237fad929a25e809a2d0e9_131.png)

![](img/034363744f237fad929a25e809a2d0e9_133.png)

我们了解到：
1.  Series是一个**一维**的数据结构，可视为带标签的数组。
2.  创建Series有三种主要方式：使用**列表**、**NumPy数组**和**字典**。
3.  可以通过`index`参数设置**显示索引**，提高数据可读性。
4.  其**索引、切片**操作类似一维数组。
5.  掌握了`shape`、`size`、`values`等常用**属性**。
6.  学会了`head()`、`tail()`、`unique()`、`isnull()`等常用**方法**。
7.  理解了Series运算的规则：**索引对齐**。

![](img/034363744f237fad929a25e809a2d0e9_135.png)

Series是构成更复杂数据结构DataFrame的基础。下一节，我们将学习如何由多个Series组成功能更强大的**DataFrame**。