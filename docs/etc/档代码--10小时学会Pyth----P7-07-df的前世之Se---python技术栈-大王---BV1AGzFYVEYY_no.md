# Python金融量化：P7：07 df的前世之Series 📊

![](img/0bbc97bed0d63fb1401977b353131587_1.png)

## 概述
在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——**Series**。我们将了解它的作用、如何创建它、以及它的基本属性和操作方法。理解Series是学习更复杂的DataFrame结构的基础。

![](img/0bbc97bed0d63fb1401977b353131587_3.png)

---

## 为什么需要Pandas？🤔
上一节我们回顾了NumPy模块，它主要用于存储和处理**数值型数据**。但在实际数据分析中，我们经常需要处理大量的**非数值型数据**，例如字符串。Pandas模块就是为了高效处理这类数据而设计的。

![](img/0bbc97bed0d63fb1401977b353131587_5.png)

简单来说：
*   **NumPy**：擅长处理**数值型**数据。
*   **Pandas**：擅长处理**非数值型**数据（当然也能处理数值型数据）。

---

![](img/0bbc97bed0d63fb1401977b353131587_7.png)

![](img/0bbc97bed0d63fb1401977b353131587_9.png)

## Pandas中的两个核心类 📚
Pandas模块主要围绕两个核心的**数据结构**（即数据容器）展开：
1.  **Series**
2.  **DataFrame**

![](img/0bbc97bed0d63fb1401977b353131587_11.png)

本节中，我们先来学习**Series**。

![](img/0bbc97bed0d63fb1401977b353131587_13.png)

---

## 什么是Series？🔍
我们可以将Series暂时理解为一个**一维数组**。它由两部分组成：
*   **values**：存储的数据元素。
*   **index**：每个数据元素对应的索引。

![](img/0bbc97bed0d63fb1401977b353131587_15.png)

其结构可以形象地表示为：
```
index | values
------|-------
0     | 数据1
1     | 数据2
2     | 数据3
```

---

![](img/0bbc97bed0d63fb1401977b353131587_17.png)

![](img/0bbc97bed0d63fb1401977b353131587_19.png)

## 创建Series 🛠️
首先，我们需要导入Series类。

![](img/0bbc97bed0d63fb1401977b353131587_21.png)

![](img/0bbc97bed0d63fb1401977b353131587_23.png)

```python
from pandas import Series
```

![](img/0bbc97bed0d63fb1401977b353131587_25.png)

以下是创建Series的几种常用方法：

![](img/0bbc97bed0d63fb1401977b353131587_27.png)

![](img/0bbc97bed0d63fb1401977b353131587_29.png)

### 1. 使用列表作为数据源
列表中的元素将成为Series的`values`。

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，左侧是默认的**隐式索引**（0, 1, 2, 3），右侧是数据值。

![](img/0bbc97bed0d63fb1401977b353131587_31.png)

![](img/0bbc97bed0d63fb1401977b353131587_33.png)

### 2. 使用NumPy数组作为数据源
注意：Series是**一维**结构，因此数据源也必须是**一维**数组。

![](img/0bbc97bed0d63fb1401977b353131587_35.png)

![](img/0bbc97bed0d63fb1401977b353131587_37.png)

```python
import numpy as np
data = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data)
print(s)
```

![](img/0bbc97bed0d63fb1401977b353131587_39.png)

![](img/0bbc97bed0d63fb1401977b353131587_41.png)

### 3. 使用字典作为数据源
字典的`key`将成为Series的**显示索引**，`value`将成为`values`。

![](img/0bbc97bed0d63fb1401977b353131587_43.png)

![](img/0bbc97bed0d63fb1401977b353131587_45.png)

```python
dic = {'语文': 100, '数学': 99, '理综': 250}
s = Series(data=dic)
print(s)
```

![](img/0bbc97bed0d63fb1401977b353131587_47.png)

### 4. 指定显示索引 (index参数)
我们可以使用`index`参数自定义索引，这能**大大增强数据的可读性**。

![](img/0bbc97bed0d63fb1401977b353131587_49.png)

![](img/0bbc97bed0d63fb1401977b353131587_51.png)

![](img/0bbc97bed0d63fb1401977b353131587_53.png)

![](img/0bbc97bed0d63fb1401977b353131587_55.png)

```python
s = Series(data=[1, 2, 3, 4], index=['A', 'B', 'C', 'D'])
print(s)
```
此时，索引不再是0,1,2,3，而是我们指定的’A’, ‘B’, ‘C’, ‘D’。

![](img/0bbc97bed0d63fb1401977b353131587_57.png)

![](img/0bbc97bed0d63fb1401977b353131587_59.png)

---

![](img/0bbc97bed0d63fb1401977b353131587_61.png)

![](img/0bbc97bed0d63fb1401977b353131587_63.png)

![](img/0bbc97bed0d63fb1401977b353131587_65.png)

## Series的索引与切片 🔪
Series的索引和切片操作与一维数组非常相似。

![](img/0bbc97bed0d63fb1401977b353131587_67.png)

![](img/0bbc97bed0d63fb1401977b353131587_69.png)

假设有Series `s`：
```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])
```

![](img/0bbc97bed0d63fb1401977b353131587_71.png)

![](img/0bbc97bed0d63fb1401977b353131587_73.png)

**索引**：
*   通过位置索引：`s[0]` 返回 `100`。
*   通过显示索引：`s[‘语文’]` 或 `s.语文` 返回 `100`。

![](img/0bbc97bed0d63fb1401977b353131587_75.png)

![](img/0bbc97bed0d63fb1401977b353131587_77.png)

**切片**：
*   `s[0:2]` 返回索引0和1对应的数据。

![](img/0bbc97bed0d63fb1401977b353131587_79.png)

![](img/0bbc97bed0d63fb1401977b353131587_81.png)

---

![](img/0bbc97bed0d63fb1401977b353131587_83.png)

## Series的常用属性 📊
以下是Series对象的一些常用属性：

![](img/0bbc97bed0d63fb1401977b353131587_85.png)

![](img/0bbc97bed0d63fb1401977b353131587_87.png)

![](img/0bbc97bed0d63fb1401977b353131587_89.png)

```python
s = Series(data=[100, 99, 250], index=['语文', '数学', '理综'])

![](img/0bbc97bed0d63fb1401977b353131587_91.png)

![](img/0bbc97bed0d63fb1401977b353131587_93.png)

print(s.shape)    # 返回形状，例如 (3,)
print(s.size)     # 返回元素个数，例如 3
print(s.index)    # 返回索引对象
print(s.values)   # 返回数值数组
print(s.dtype)    # 返回数据类型
```
**注意**：Series中存储的数据类型必须是统一的。如果混用数字和字符串，所有数据会被转换为`object`（字符串）类型。

![](img/0bbc97bed0d63fb1401977b353131587_95.png)

![](img/0bbc97bed0d63fb1401977b353131587_97.png)

---

![](img/0bbc97bed0d63fb1401977b353131587_99.png)

## Series的常用方法 ⚙️
以下是Series的一些常用方法：

![](img/0bbc97bed0d63fb1401977b353131587_101.png)

### 查看数据
```python
s.head(3)  # 查看前3条数据
s.tail(3)  # 查看后3条数据
```

![](img/0bbc97bed0d63fb1401977b353131587_103.png)

![](img/0bbc97bed0d63fb1401977b353131587_105.png)

### 数据去重
```python
s.unique() # 返回去重后的数组
```

![](img/0bbc97bed0d63fb1401977b353131587_107.png)

![](img/0bbc97bed0d63fb1401977b353131587_109.png)

![](img/0bbc97bed0d63fb1401977b353131587_111.png)

### 判断空值
在数据运算中可能产生空值（NaN）。
```python
s.isnull()   # 判断每个元素是否为空，返回布尔Series
s.notnull()  # 判断每个元素是否非空，返回布尔Series
```

![](img/0bbc97bed0d63fb1401977b353131587_113.png)

![](img/0bbc97bed0d63fb1401977b353131587_115.png)

![](img/0bbc97bed0d63fb1401977b353131587_117.png)

### 算术运算
Series支持直接的算术运算符（`+`, `-`, `*`, `/`），也对应有方法（`add()`, `sub()`, `mul()`, `div()`）。
**运算规则**：**索引一致的元素才会进行运算**，索引不一致的结果会填充为空值（NaN）。

![](img/0bbc97bed0d63fb1401977b353131587_119.png)

![](img/0bbc97bed0d63fb1401977b353131587_121.png)

![](img/0bbc97bed0d63fb1401977b353131587_123.png)

示例：
```python
s1 = Series([1, 2, 3], index=[‘A‘, ’B‘, ’C‘])
s2 = Series([1, 2, 3], index=[‘A‘, ’C‘, ’D‘])
result = s1 + s2
print(result)
```
输出中，只有索引’A’和’C’能对应相加，索引’B’和’D’由于在另一个Series中没有匹配项，结果即为NaN。

![](img/0bbc97bed0d63fb1401977b353131587_125.png)

![](img/0bbc97bed0d63fb1401977b353131587_127.png)

![](img/0bbc97bed0d63fb1401977b353131587_129.png)

![](img/0bbc97bed0d63fb1401977b353131587_131.png)

---

![](img/0bbc97bed0d63fb1401977b353131587_133.png)

![](img/0bbc97bed0d63fb1401977b353131587_135.png)

## 总结 🎯
本节课我们一起学习了Pandas中的**Series**数据结构：
1.  **Series**是一个**一维**的数据容器，由`values`和`index`组成。
2.  可以使用**列表、NumPy数组、字典**来创建Series。
3.  通过`index`参数可以设置**显示索引**，提高数据可读性。
4.  掌握了Series的**索引、切片、常用属性和方法**。
5.  理解了Series运算的核心规则：**按索引对齐进行运算**。

![](img/0bbc97bed0d63fb1401977b353131587_137.png)

Series是构成更强大的DataFrame的基石。下一节，我们将学习如何由多个Series组成**DataFrame**，它将是我们在数据分析中最为得力的工具。