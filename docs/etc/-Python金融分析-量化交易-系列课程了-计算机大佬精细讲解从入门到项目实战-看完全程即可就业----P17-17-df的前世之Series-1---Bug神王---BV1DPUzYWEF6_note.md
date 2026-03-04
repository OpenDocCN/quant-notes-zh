# Python金融分析+量化交易：P17：Series数据结构详解 📊

![](img/116a05347eaf03f91fd82468754c90b0_1.png)

## 概述
在本节课中，我们将要学习Pandas模块中的核心数据结构之一：**Series**。我们将了解为什么需要Pandas来处理非数值型数据，并详细学习Series的创建、属性、方法以及基本操作。

![](img/116a05347eaf03f91fd82468754c90b0_3.png)

---

![](img/116a05347eaf03f91fd82468754c90b0_5.png)

## 为什么需要Pandas？🤔
上一节我们介绍了NumPy模块，它主要用于处理数值型数据。然而，在数据分析中，我们经常需要处理大量的非数值型数据，例如字符串。Pandas模块正是为此而生，它主要用于存储和处理非数值型数据。

Pandas模块中，我们需要重点学习两个类：**Series**和**DataFrame**。它们是两种不同的数据结构，可以装载数据，并基于其封装好的工具类和方法来处理数据。

![](img/116a05347eaf03f91fd82468754c90b0_7.png)

![](img/116a05347eaf03f91fd82468754c90b0_9.png)

---

![](img/116a05347eaf03f91fd82468754c90b0_11.png)

## Series简介 📈
本节中我们来看看Series。Series是Pandas中的一种数据结构，我们可以暂且将其理解为一个**一维数组**。它由两部分组成：
*   **values**：存储的数据元素。
*   **index**：每个数据元素对应的索引。

![](img/116a05347eaf03f91fd82468754c90b0_13.png)

Series是构成更复杂数据结构DataFrame的基本元素。理解Series是学习DataFrame的基础。

![](img/116a05347eaf03f91fd82468754c90b0_15.png)

---

## 创建Series 🛠️
以下是创建Series的几种常用方法。首先，我们需要导入Series类。

![](img/116a05347eaf03f91fd82468754c90b0_17.png)

```python
from pandas import Series
```

![](img/116a05347eaf03f91fd82468754c90b0_19.png)

![](img/116a05347eaf03f91fd82468754c90b0_21.png)

![](img/116a05347eaf03f91fd82468754c90b0_23.png)

### 1. 使用列表创建
我们可以使用列表作为数据源来创建Series。

![](img/116a05347eaf03f91fd82468754c90b0_25.png)

![](img/116a05347eaf03f91fd82468754c90b0_27.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
运行结果会显示一个Series，左侧是默认的隐式索引（0, 1, 2, 3），右侧是数据值（1, 2, 3, 4）。

![](img/116a05347eaf03f91fd82468754c90b0_29.png)

### 2. 使用NumPy数组创建
我们也可以使用一维的NumPy数组作为数据源。需要注意的是，Series是一维结构，因此数据源也必须是一维的。

![](img/116a05347eaf03f91fd82468754c90b0_31.png)

```python
import numpy as np
data = np.random.randint(0, 100, size=4)
s = Series(data=data)
print(s)
```

![](img/116a05347eaf03f91fd82468754c90b0_33.png)

![](img/116a05347eaf03f91fd82468754c90b0_35.png)

### 3. 使用字典创建
使用字典创建Series时，字典的键（key）会成为Series的显示索引，字典的值（value）会成为Series存储的数据。

![](img/116a05347eaf03f91fd82468754c90b0_37.png)

![](img/116a05347eaf03f91fd82468754c90b0_39.png)

![](img/116a05347eaf03f91fd82468754c90b0_41.png)

```python
dic = {'语文': 100, '数学': 99, '理综': 250}
s = Series(data=dic)
print(s)
```

![](img/116a05347eaf03f91fd82468754c90b0_43.png)

![](img/116a05347eaf03f91fd82468754c90b0_45.png)

### 4. 指定显示索引
在创建Series时，我们可以通过`index`参数来指定显示索引，这可以大大增强数据的可读性。

![](img/116a05347eaf03f91fd82468754c90b0_47.png)

![](img/116a05347eaf03f91fd82468754c90b0_49.png)

```python
s = Series(data=[1, 2, 3, 4], index=['A', 'B', 'C', 'D'])
print(s)
```
此时，索引不再是0,1,2,3，而是我们指定的A, B, C, D。

![](img/116a05347eaf03f91fd82468754c90b0_51.png)

![](img/116a05347eaf03f91fd82468754c90b0_53.png)

![](img/116a05347eaf03f91fd82468754c90b0_55.png)

![](img/116a05347eaf03f91fd82468754c90b0_57.png)

---

![](img/116a05347eaf03f91fd82468754c90b0_59.png)

![](img/116a05347eaf03f91fd82468754c90b0_61.png)

![](img/116a05347eaf03f91fd82468754c90b0_63.png)

## Series的索引与切片 🔍
Series的索引和切片操作与一维数组非常相似。

![](img/116a05347eaf03f91fd82468754c90b0_65.png)

![](img/116a05347eaf03f91fd82468754c90b0_67.png)

![](img/116a05347eaf03f91fd82468754c90b0_69.png)

*   **索引**：可以通过位置（隐式索引）或显示索引来获取值。
    ```python
    s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
    print(s[0])        # 输出: 100
    print(s['语文'])   # 输出: 100
    print(s.语文)      # 输出: 100 (当索引是有效的Python变量名时可用)
    ```
*   **切片**：与列表切片规则相同。
    ```python
    print(s[0:2]) # 输出索引0和1对应的值
    ```

![](img/116a05347eaf03f91fd82468754c90b0_71.png)

![](img/116a05347eaf03f91fd82468754c90b0_73.png)

---

![](img/116a05347eaf03f91fd82468754c90b0_75.png)

![](img/116a05347eaf03f91fd82468754c90b0_77.png)

![](img/116a05347eaf03f91fd82468754c90b0_79.png)

## Series的常用属性 📋
以下是Series对象的一些常用属性：

![](img/116a05347eaf03f91fd82468754c90b0_81.png)

![](img/116a05347eaf03f91fd82468754c90b0_83.png)

![](img/116a05347eaf03f91fd82468754c90b0_85.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
print(s.shape)    # 形状，例如 (3,)
print(s.size)     # 元素个数，例如 3
print(s.index)    # 索引，例如 Index(['语文', '数学', '理综'])
print(s.values)   # 值，例如 array([100,  99, 250])
print(s.dtype)    # 元素的数据类型，例如 int64
```
**注意**：Series中存储的数据类型必须是统一的。如果创建时传入不同类型的数据（如整数和字符串），Pandas会尝试将其统一为一种类型（通常是`object`，即字符串）。

![](img/116a05347eaf03f91fd82468754c90b0_87.png)

---

![](img/116a05347eaf03f91fd82468754c90b0_89.png)

![](img/116a05347eaf03f91fd82468754c90b0_91.png)

## Series的常用方法 ⚙️
以下是Series对象的一些常用方法：

![](img/116a05347eaf03f91fd82468754c90b0_93.png)

![](img/116a05347eaf03f91fd82468754c90b0_95.png)

![](img/116a05347eaf03f91fd82468754c90b0_97.png)

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))

![](img/116a05347eaf03f91fd82468754c90b0_99.png)

![](img/116a05347eaf03f91fd82468754c90b0_101.png)

print(s.head(3))   # 显示前3个元素
print(s.tail(3))   # 显示后3个元素
print(s.unique())  # 返回去重后的元素数组
```

![](img/116a05347eaf03f91fd82468754c90b0_103.png)

### 空值检测
`isnull()`和`notnull()`方法用于检测Series中的空值（NaN）。

![](img/116a05347eaf03f91fd82468754c90b0_105.png)

![](img/116a05347eaf03f91fd82468754c90b0_107.png)

![](img/116a05347eaf03f91fd82468754c90b0_109.png)

```python
s1 = Series(data=[1, 2, 3], index=['A', 'B', 'C'])
s2 = Series(data=[1, 2, 3], index=['A', 'C', 'D'])
s = s1 + s2
print(s)
# 输出可能为：
# A    2.0
# B    NaN
# C    6.0
# D    NaN

![](img/116a05347eaf03f91fd82468754c90b0_111.png)

![](img/116a05347eaf03f91fd82468754c90b0_113.png)

![](img/116a05347eaf03f91fd82468754c90b0_115.png)

print(s.isnull())  # 判断每个元素是否为空
print(s.notnull()) # 判断每个元素是否非空
```
**算术运算法则**：两个Series进行算术运算时，**只有索引一致的元素才会进行运算**，索引不一致的元素结果会填充为空值（NaN）。

![](img/116a05347eaf03f91fd82468754c90b0_117.png)

![](img/116a05347eaf03f91fd82468754c90b0_119.png)

![](img/116a05347eaf03f91fd82468754c90b0_121.png)

![](img/116a05347eaf03f91fd82468754c90b0_123.png)

---

![](img/116a05347eaf03f91fd82468754c90b0_125.png)

![](img/116a05347eaf03f91fd82468754c90b0_127.png)

![](img/116a05347eaf03f91fd82468754c90b0_129.png)

## 总结 🎯
本节课中我们一起学习了Pandas的核心数据结构**Series**。

![](img/116a05347eaf03f91fd82468754c90b0_131.png)

![](img/116a05347eaf03f91fd82468754c90b0_133.png)

![](img/116a05347eaf03f91fd82468754c90b0_135.png)

我们了解到：
1.  Series是一个类似于一维数组的数据结构，由**值（values）**和**索引（index）**组成。
2.  可以通过**列表、NumPy数组、字典**等多种方式创建Series。
3.  可以使用`index`参数指定**显示索引**，以提高数据的可读性。
4.  Series支持类似一维数组的**索引和切片**操作。
5.  掌握了Series的常用**属性**（如`shape`, `size`, `values`, `dtype`）和**方法**（如`head()`, `tail()`, `unique()`, `isnull()`）。
6.  两个Series进行算术运算时，遵循**索引对齐**的原则，只有索引相同的元素才会参与计算。

![](img/116a05347eaf03f91fd82468754c90b0_137.png)

![](img/116a05347eaf03f91fd82468754c90b0_139.png)

Series是构建更强大的DataFrame的基石。下一节，我们将学习由多个Series组成的二维数据结构——**DataFrame**。