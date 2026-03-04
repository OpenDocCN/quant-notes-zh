# Python金融量化+业务数据分析：P7：1.df的前世之Series

![](img/d87909da026b6c3e04a7ccd3c0c21082_1.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解为什么需要Pandas，以及如何创建、索引和操作Series对象。

## 概述：为什么需要Pandas？

![](img/d87909da026b6c3e04a7ccd3c0c21082_3.png)

上一节我们介绍了NumPy模块，它主要用于存储和处理数值型数据。然而，在数据分析中，我们经常需要处理大量的非数值型数据，例如字符串。Pandas模块正是为了高效处理这类非数值型数据而设计的。

简单来说：
*   **NumPy**：主要处理**数值型**数据。
*   **Pandas**：主要处理**非数值型**数据（当然也能处理数值型数据）。

![](img/d87909da026b6c3e04a7ccd3c0c21082_5.png)

## Pandas中的两个核心类

在Pandas模块中，我们将重点学习两个常用的类，它们也是两种核心的数据结构：
1.  **Series**
2.  **DataFrame**

![](img/d87909da026b6c3e04a7ccd3c0c21082_7.png)

数据结构可以理解为一种容器，用于装载数据。Series和DataFrame就是Pandas中用来装载和处理数据的容器。

![](img/d87909da026b6c3e04a7ccd3c0c21082_9.png)

本节我们先来学习Series，它是理解更复杂的DataFrame的基础，因为DataFrame本质上是由多个Series组成的。

![](img/d87909da026b6c3e04a7ccd3c0c21082_11.png)

## 什么是Series？📊

![](img/d87909da026b6c3e04a7ccd3c0c21082_13.png)

我们可以将Series暂且理解为一个**一维数组**。它由两部分组成：
*   **values**：存储的数据元素。
*   **index**：每个数据元素对应的索引。

![](img/d87909da026b6c3e04a7ccd3c0c21082_15.png)

其结构可以表示为：
```
index | values
------|-------
0     | 数据1
1     | 数据2
2     | 数据3
```

## 创建Series

首先，我们需要导入Series类。

![](img/d87909da026b6c3e04a7ccd3c0c21082_17.png)

```python
from pandas import Series
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_19.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_21.png)

创建Series主要使用其构造方法 `Series()`。以下是几种常见的创建方式。

![](img/d87909da026b6c3e04a7ccd3c0c21082_23.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_25.png)

### 使用列表创建

![](img/d87909da026b6c3e04a7ccd3c0c21082_27.png)

列表可以作为`data`参数传入，创建Series。

![](img/d87909da026b6c3e04a7ccd3c0c21082_29.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，左侧是默认的隐式索引（0, 1, 2, 3），右侧是存储的值。

### 使用NumPy数组创建

![](img/d87909da026b6c3e04a7ccd3c0c21082_31.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_33.png)

需要注意的是，Series是一维结构，因此数据源也应当是一维的。

![](img/d87909da026b6c3e04a7ccd3c0c21082_35.png)

```python
import numpy as np
# 使用一维数组
data = np.random.randint(0, 100, size=5)
s = Series(data=data)
print(s)
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_37.png)

### 使用字典创建

![](img/d87909da026b6c3e04a7ccd3c0c21082_39.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_41.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_43.png)

使用字典创建Series时，字典的键（key）会自动成为Series的显示索引，值（value）成为存储的数据。

![](img/d87909da026b6c3e04a7ccd3c0c21082_45.png)

```python
dic = {‘语文‘: 100, ‘数学‘: 99, ‘理综‘: 250}
s = Series(data=dic)
print(s)
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_47.png)

### 指定显示索引

![](img/d87909da026b6c3e04a7ccd3c0c21082_49.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_51.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_53.png)

在创建时，可以通过`index`参数指定显示索引，这能大大增强数据的可读性。

![](img/d87909da026b6c3e04a7ccd3c0c21082_55.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_57.png)

```python
s = Series(data=[1, 2, 3, 4], index=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(s)
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_59.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_61.png)

**为什么需要显示索引？**
默认的隐式索引（0,1,2...）可读性不强。例如，用`[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘]`作为索引，可以清晰地表明每个分数对应的科目。

![](img/d87909da026b6c3e04a7ccd3c0c21082_63.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_65.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_67.png)

## Series的索引与切片

![](img/d87909da026b6c3e04a7ccd3c0c21082_69.png)

Series的索引和切片操作与一维数组非常相似。

![](img/d87909da026b6c3e04a7ccd3c0c21082_71.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_73.png)

以下是索引和切片操作的示例：
```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
# 索引
print(s[0]) # 输出：100
print(s[‘语文‘]) # 输出：100
print(s.语文) # 输出：100 (通过点操作符访问显示索引)

![](img/d87909da026b6c3e04a7ccd3c0c21082_75.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_77.png)

# 切片
print(s[0:2]) # 输出前两个元素
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_79.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_81.png)

## Series的常用属性

![](img/d87909da026b6c3e04a7ccd3c0c21082_83.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_85.png)

Series对象提供了一些有用的属性来获取其信息。

![](img/d87909da026b6c3e04a7ccd3c0c21082_87.png)

以下是几个常用属性：
*   `shape`: 返回Series的形状（元素个数）。
*   `size`: 返回Series中元素的个数。
*   `index`: 返回Series的索引。
*   `values`: 返回Series存储的值（一个数组）。
*   `dtype`: 返回Series中元素的数据类型。

![](img/d87909da026b6c3e04a7ccd3c0c21082_89.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_91.png)

```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
print(s.shape)   # 输出：(3,)
print(s.size)    # 输出：3
print(s.index)   # 输出：Index([‘语文‘, ‘数学‘, ‘理综‘], dtype=‘object‘)
print(s.values)  # 输出：[100  99 250]
print(s.dtype)   # 输出：int64
```
**注意**：Series中存储的数据类型必须是统一的。如果混用不同类型（如整数和字符串），Pandas会尝试进行统一转换。

![](img/d87909da026b6c3e04a7ccd3c0c21082_93.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_95.png)

## Series的常用方法

![](img/d87909da026b6c3e04a7ccd3c0c21082_97.png)

Series还封装了一些实用的方法。

![](img/d87909da026b6c3e04a7ccd3c0c21082_99.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_101.png)

以下是几个常用方法：
*   `head(n)`: 显示前n个元素，默认n=5。
*   `tail(n)`: 显示后n个元素，默认n=5。
*   `unique()`: 返回去重后的元素。
*   `isnull()`: 判断每个元素是否为空（NaN），返回布尔序列。
*   `notnull()`: 判断每个元素是否非空，返回布尔序列。

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))
print(s.head(3)) # 查看前3个
print(s.tail(3)) # 查看后3个
print(s.unique()) # 去重
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_103.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_105.png)

### 关于空值（NaN）的检测

![](img/d87909da026b6c3e04a7ccd3c0c21082_107.png)

在Series运算中可能产生空值。例如，两个索引不完全一致的Series相加时，索引不匹配的位置会填充为`NaN`（Not a Number）。

![](img/d87909da026b6c3e04a7ccd3c0c21082_109.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_111.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_113.png)

```python
s1 = Series(data=[1, 2, 3], index=[‘A‘, ‘B‘, ‘C‘])
s2 = Series(data=[1, 2, 3], index=[‘A‘, ‘C‘, ‘D‘])
s = s1 + s2
print(s)
# 输出：
# A    2.0
# B    NaN
# C    6.0
# D    NaN
# dtype: float64

![](img/d87909da026b6c3e04a7ccd3c0c21082_115.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_117.png)

print(s.isnull()) # 检测哪些位置是NaN
```

![](img/d87909da026b6c3e04a7ccd3c0c21082_119.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_121.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_123.png)

## Series的运算规则

![](img/d87909da026b6c3e04a7ccd3c0c21082_125.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_127.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_129.png)

从上面的例子可以看出，Series进行算术运算（如加法）时，遵循一个核心规则：**只有索引一致的元素才会进行运算**。对于索引不匹配的位置，结果会用`NaN`填充。

![](img/d87909da026b6c3e04a7ccd3c0c21082_131.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_133.png)

## 总结

![](img/d87909da026b6c3e04a7ccd3c0c21082_135.png)

![](img/d87909da026b6c3e04a7ccd3c0c21082_137.png)

本节课我们一起学习了Pandas中的Series数据结构。
*   我们了解了Pandas主要用于处理非数值型数据。
*   我们学习了Series是一个一维数据结构，由`values`和`index`组成。
*   我们掌握了使用列表、NumPy数组和字典创建Series的多种方法，并学会了通过`index`参数指定有意义的显示索引。
*   我们熟悉了Series的索引、切片操作以及`shape`、`values`等常用属性。
*   我们了解了`head()`、`unique()`、`isnull()`等常用方法。
*   最后，我们明确了Series运算的核心规则：按索引对齐运算。

![](img/d87909da026b6c3e04a7ccd3c0c21082_139.png)

Series是构建更复杂数据表格DataFrame的基石。下一节，我们将学习DataFrame，看看如何将多个Series组合成一个二维表格。