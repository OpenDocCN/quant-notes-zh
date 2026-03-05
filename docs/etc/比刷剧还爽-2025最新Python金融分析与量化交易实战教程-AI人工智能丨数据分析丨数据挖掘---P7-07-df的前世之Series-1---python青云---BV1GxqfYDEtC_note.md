# Python金融量化：P7：07 Series数据结构入门

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_1.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解它是什么、为什么需要它，以及如何创建和使用它。

## 概述：为什么需要Pandas？

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_3.png)

在前面的小节中，我们已经学习了NumPy模块。NumPy是一个基于一维或多维数组的模块，主要用于存储和处理**数值型**数据。

既然NumPy已经可以处理数值型数据，为什么还需要学习Pandas呢？原因在于，在数据分析中，除了数值型数据，我们还需要处理大量的**非数值型**数据，例如字符串。Pandas模块就是专门用来存储和处理非数值型数据的。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_5.png)

简单来说：
*   NumPy：主要处理**数值型**数据。
*   Pandas：主要处理**非数值型**数据（当然也能处理数值型数据）。

## Pandas中的两个核心类

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_7.png)

在Pandas模块中，我们需要重点学习两个常用的类，它们也是两种不同的数据结构：
1.  **Series**
2.  **DataFrame**

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_9.png)

数据结构可以理解为一种容器，用于装载数据。Pandas中的这两个类就是用来装载非数值型数据，并利用它们封装好的方法和属性来处理这些数据。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_11.png)

上一节我们介绍了Pandas的定位，本节中我们来看看第一个核心结构Series。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_13.png)

## 什么是Series？📊

Series是Pandas中的一种数据结构。我们可以暂且把它理解为一个**一维数组**。它由两部分组成：
1.  **values**：存储的数据元素。
2.  **index**：每个数据元素对应的索引。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_15.png)

**公式表示**：`Series = index + values`

## 如何创建Series？

在创建Series之前，需要先导入它。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_17.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_19.png)

```python
from pandas import Series
```

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_21.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_23.png)

导入后，就可以使用Series的构造方法来创建对象了。构造方法有几个常用参数：
*   `data`：数据源。
*   `index`：显示索引（可选）。
*   `dtype`：数据类型（可选）。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_25.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_27.png)

以下是几种创建Series的常用方式：

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_29.png)

### 1. 使用列表作为数据源

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
运行结果中，第一列是索引（默认从0开始，称为隐式索引），第二列是存储的值。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_31.png)

### 2. 使用NumPy数组作为数据源

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_33.png)

Series是**一维**数据结构，因此数据源也必须是一维的。
```python
import numpy as np
data = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data)
print(s)
```

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_35.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_37.png)

### 3. 使用字典作为数据源

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_39.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_41.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_43.png)

使用字典时，字典的**键（key）** 会成为Series的**显示索引**，字典的**值（value）** 会成为Series存储的数据。
```python
dic = {'语文': 100, '数学': 99, '理综': 250}
s = Series(data=dic)
print(s)
```

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_45.png)

### 4. 指定显示索引（index参数）

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_47.png)

我们可以通过`index`参数自定义索引，这能大大增强数据的可读性。
```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
print(s)
```
显示索引（如‘语文’）比隐式索引（0,1,2）更能直观表达数据的含义。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_49.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_51.png)

## Series的索引与切片

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_53.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_55.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_57.png)

Series的索引和切片操作与一维数组非常相似。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_59.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_61.png)

假设有Series对象`s`：
```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
```

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_63.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_65.png)

**索引**：
*   通过位置索引：`s[0]` 返回 `100`。
*   通过显示索引：`s[‘语文’]` 或 `s.语文` 返回 `100`。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_67.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_69.png)

**切片**：
*   `s[0:2]` 返回索引0和1对应的元素。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_71.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_73.png)

## Series的常用属性

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_75.png)

以下是Series对象的一些常用属性：

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_77.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_79.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_81.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_83.png)

print(s.shape)    # 形状，例如 (3,)
print(s.size)     # 元素个数，例如 3
print(s.index)    # 索引，例如 Index(['语文', '数学', '理综'], dtype='object')
print(s.values)   # 值组成的数组，例如 [100  99 250]
print(s.dtype)    # 元素的数据类型，例如 int64
```
**注意**：Series中存储的数据类型必须是统一的。如果混用数字和字符串，所有数据会被统一为`object`（字符串）类型。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_85.png)

## Series的常用方法

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_87.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_89.png)

以下是Series对象的一些常用方法：

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_91.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_93.png)

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_95.png)

print(s.head(3))  # 显示前3个元素
print(s.tail(3))  # 显示后3个元素
print(s.unique()) # 去重，返回唯一值数组
```

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_97.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_99.png)

### 空值检测方法

在数据处理中，经常需要检测空值。
*   `isna()`: 判断每个元素是否为空值，是则返回`True`。
*   `notna()`: 判断每个元素是否为非空值，是则返回`True`。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_101.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_103.png)

空值通常出现在数据运算中。例如，两个Series相加时，**只有索引相同的元素才会进行运算**，索引不同的元素结果会填充为空值（NaN）。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_105.png)

```python
s1 = Series(data=[1, 2, 3], index=['A', 'B', 'C'])
s2 = Series(data=[1, 2, 3], index=['A', 'C', 'D'])
s = s1 + s2
print(s)
print(s.isna())
```
运行结果中，只有索引’A’和’C’对应的值相加成功，索引’B’和’D’由于在另一个Series中没有匹配项，结果为空值（NaN）。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_107.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_109.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_111.png)

### 算术运算方法

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_113.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_115.png)

Series支持直接的算术运算符（`+`, `-`, `*`, `/`），也对应有方法（`add()`, `sub()`, `mul()`, `div()`），通常直接使用运算符更简便。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_117.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_119.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_121.png)

## Series运算的核心法则

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_123.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_125.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_127.png)

从上面的例子可以总结出Series算术运算的核心法则：
**索引一致的元素才能进行算术运算，否则结果位置填充为空值（NaN）。**

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_129.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_131.png)

## 总结与过渡

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_133.png)

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_135.png)

本节课中我们一起学习了Pandas的第一个核心数据结构Series。我们了解到：
*   Series是一个**一维**的、带索引的数据容器。
*   可以使用列表、NumPy数组、字典来创建Series。
*   **显示索引**能提高数据的可读性。
*   掌握了Series的索引、切片、常用属性和方法。
*   Series运算遵循**索引对齐**原则。

![](img/ede7f1e8c987c467b85dbe58fcdeefb9_137.png)

Series本身可以独立使用，但它更重要的角色是作为DataFrame的组成部分。你可以把Series想象成Excel表中的一列数据。那么，由多个Series（多列）组合而成的DataFrame，就是一个二维表格。下一节，我们将进入Pandas最核心的部分——DataFrame的学习。