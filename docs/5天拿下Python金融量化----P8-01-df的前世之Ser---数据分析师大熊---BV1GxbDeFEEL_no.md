# Python金融量化：P8：01 df的前世之Series 📊

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_1.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解它是什么、如何创建它，以及如何使用其基本属性和方法。理解Series是后续学习更复杂的DataFrame结构的基础。

---

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_3.png)

## 为什么需要Pandas？🤔

上一节我们介绍了NumPy模块，它主要用于存储和处理**数值型数据**。但在实际数据分析中，我们经常需要处理大量的**非数值型数据**，例如字符串。Pandas模块正是为此而生，它专门用于高效地存储和处理非数值型数据。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_5.png)

当然，Pandas也可以处理数值型数据，但其核心优势在于处理混合类型的数据。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_7.png)

---

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_9.png)

## Pandas中的两个核心类 📦

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_11.png)

在Pandas模块中，我们将重点学习两个常用的类（数据结构）：
*   **Series**
*   **DataFrame**

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_13.png)

数据结构可以理解为一种“容器”，用于装载数据。Series和DataFrame就是Pandas中用于装载数据的两种容器，它们提供了丰富的工具来处理其中存储的数据。

本节我们先从Series学起，因为它是构成DataFrame的基本元素。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_15.png)

---

## 什么是Series？🔍

我们可以将Series暂时理解为一个**一维数组**。它由两部分组成：
1.  **`values`**：存储的数据元素。
2.  **`index`**：每个数据元素对应的索引。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_17.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_19.png)

---

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_21.png)

## 如何创建Series？🛠️

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_23.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_25.png)

在创建Series之前，需要先导入它。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_27.png)

```python
from pandas import Series
```

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_29.png)

创建Series主要通过调用其构造方法 `Series()` 来实现。以下是几种常见的数据源创建方式。

### 使用列表创建
列表是最简单的数据源。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_31.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_33.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，左侧是默认的**隐式索引**（0, 1, 2, 3），右侧是数据值。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_35.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_37.png)

### 使用NumPy数组创建
Series只能是一维结构，因此数据源也必须是一维的。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_39.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_41.png)

```python
import numpy as np
data = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data)
print(s)
```

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_43.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_45.png)

### 使用字典创建
使用字典创建时，字典的键（key）会自动成为Series的**显示索引**，值（value）成为数据。

```python
dic = {'语文': 100, '数学': 99, '理综': 250}
s = Series(data=dic)
print(s)
```

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_47.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_49.png)

### 指定显示索引
在创建时，可以通过 `index` 参数自定义显示索引，这能大大增强数据的可读性。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_51.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_53.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_55.png)

```python
s = Series(data=[1, 2, 3, 4], index=['A', 'B', 'C', 'D'])
print(s)
```

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_57.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_59.png)

---

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_61.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_63.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_65.png)

## Series的索引与切片 🔪

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_67.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_69.png)

Series的索引和切片操作与一维数组非常相似。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_71.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_73.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_75.png)

# 索引
print(s[0])        # 通过隐式索引访问
print(s['语文'])   # 通过显示索引访问
print(s.语文)      # 通过“点”语法访问显示索引（索引名需符合变量命名规则）

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_77.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_79.png)

# 切片
print(s[0:2])      # 切片操作
```

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_81.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_83.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_85.png)

---

## Series的常用属性 📋

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_87.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_89.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_91.png)

以下是Series对象一些常用的属性：

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_93.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_95.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_97.png)

print(s.shape)    # 返回Series的形状（元素个数）
print(s.size)     # 返回Series中元素的个数
print(s.index)    # 返回Series的索引
print(s.values)   # 返回Series的值（构成的数据数组）
print(s.dtype)    # 返回Series中元素的数据类型
```
**注意**：Series中存储的数据类型必须是统一的。如果创建时传入混合类型（如数字和字符串），Pandas会尝试将其统一为一种类型（通常是`object`，即字符串类型）。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_99.png)

---

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_101.png)

## Series的常用方法 ⚙️

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_103.png)

以下是Series对象一些常用的方法：

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_105.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_107.png)

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_109.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_111.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_113.png)

print(s.head(3))      # 显示前3个元素
print(s.tail(3))      # 显示后3个元素
print(s.unique())     # 返回去重后的元素数组
print(s.isnull())     # 判断每个元素是否为空（NaN），返回布尔Series
print(s.notnull())    # 判断每个元素是否非空，返回布尔Series
```

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_115.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_117.png)

### 关于空值（NaN）的说明
Series在进行运算（如加法）时，遵循“索引对齐”原则：**只有索引相同的元素才会进行运算，索引不同的位置会用空值（NaN）填充**。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_119.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_121.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_123.png)

```python
s1 = Series(data=[1, 2, 3], index=['A', 'B', 'C'])
s2 = Series(data=[1, 2, 3], index=['A', 'C', 'D'])
s = s1 + s2
print(s)
print(s.isnull())
```
在这个例子中，只有索引 ‘A’ 和 ‘C’ 对齐并完成了加法，索引 ‘B’ 和 ‘D’ 由于在另一个Series中没有对应项，结果被填充为NaN。`isnull()` 和 `notnull()` 方法常用于检测这些空值。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_125.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_127.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_129.png)

---

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_131.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_133.png)

## 总结 📝

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_135.png)

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_137.png)

本节课我们一起学习了Pandas中的Series数据结构：
1.  **Series是什么**：一个类似于一维数组的数据结构，由`values`（值）和`index`（索引）组成。
2.  **如何创建Series**：可以使用列表、NumPy一维数组或字典作为数据源，并能通过`index`参数指定有意义的显示索引。
3.  **基本操作**：包括索引、切片、查看属性和调用常用方法（如`head`, `tail`, `unique`）。
4.  **运算规则**：Series间的算术运算遵循“索引对齐”原则，未对齐的索引位置会产生空值（NaN）。

![](img/c326ae9cb44f307f78eaf8892f2ca4ed_139.png)

Series是Pandas的基础，下一节我们将学习由多个Series构成的、更强大的二维数据结构——DataFrame。