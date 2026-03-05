# Python数据分析与金融量化投资：P7：01 Series的前世今生 📊

![](img/383d1ef72f2613f3059f728c0a0ae780_1.png)

## 概述
在本节课中，我们将要学习Pandas模块中的核心数据结构之一：**Series**。我们将了解为什么在NumPy之外还需要Pandas，掌握Series的创建、基本属性、索引切片以及常用方法，为后续学习更复杂的DataFrame打下坚实基础。

---

![](img/383d1ef72f2613f3059f728c0a0ae780_3.png)

## 为什么需要Pandas？🤔
上一节我们回顾了NumPy模块，它主要用于存储和处理**数值型数据**。然而，在数据分析中，我们经常需要处理大量的**非数值型数据**，例如字符串。Pandas模块正是为了高效处理这类数据而设计的。

*   **NumPy**：专注于数值型数据的数组运算。
*   **Pandas**：擅长处理包含非数值型数据的表格和序列。

![](img/383d1ef72f2613f3059f728c0a0ae780_5.png)

Pandas中两个最常用的类是 **`Series`** 和 **`DataFrame`**，它们都是可以装载数据的数据结构。本节课我们重点学习`Series`。

![](img/383d1ef72f2613f3059f728c0a0ae780_7.png)

---

![](img/383d1ef72f2613f3059f728c0a0ae780_9.png)

## 什么是Series？📈
我们可以将`Series`暂时理解为一个**一维数组**。它由两部分组成：
1.  **`values`**：存储的数据元素。
2.  **`index`**：每个数据元素对应的索引。

![](img/383d1ef72f2613f3059f728c0a0ae780_11.png)

## 创建Series 🛠️
首先，我们需要导入`Series`类。

![](img/383d1ef72f2613f3059f728c0a0ae780_13.png)

```python
from pandas import Series
```

![](img/383d1ef72f2613f3059f728c0a0ae780_15.png)

创建Series主要通过其构造方法 `Series()` 实现。以下是几种常见的数据源创建方式：

### 使用列表创建
这是最直接的方式，数据将按顺序存储，并生成默认的隐式索引（0, 1, 2...）。

```python
s1 = Series(data=[1, 2, 3, ‘four’])
print(s1)
```
**输出示例：**
```
0        1
1        2
2        3
3    four
dtype: object
```

![](img/383d1ef72f2613f3059f728c0a0ae780_17.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_18.png)

### 使用NumPy数组创建
Series可以接收一维NumPy数组作为数据源，这体现了它“一维”的特性。

![](img/383d1ef72f2613f3059f728c0a0ae780_19.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_20.png)

```python
import numpy as np
arr = np.random.randint(60, 100, size=10)
s2 = Series(data=arr)
print(s2)
```

![](img/383d1ef72f2613f3059f728c0a0ae780_22.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_23.png)

### 使用字典创建
使用字典创建时，字典的**键（key）** 会成为Series的**显示索引**，字典的**值（value）** 会成为存储的数据。

![](img/383d1ef72f2613f3059f728c0a0ae780_25.png)

```python
data_dict = {‘语文‘: 100, ‘数学‘: 99, ‘理综‘: 250}
s3 = Series(data=data_dict)
print(s3)
```
**输出示例：**
```
语文    100
数学     99
理综    250
dtype: int64
```

### 指定显示索引
在创建时，可以通过`index`参数显式地指定索引，这能极大地增强数据的可读性。

![](img/383d1ef72f2613f3059f728c0a0ae780_27.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_28.png)

```python
s4 = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
print(s4)
```

![](img/383d1ef72f2613f3059f728c0a0ae780_30.png)

**为什么需要显示索引？**
默认的隐式索引（0,1,2...）含义不明确。使用有意义的显示索引（如‘语文‘、‘数学‘）可以直接表明每个数据所代表的含义，提升代码可读性。

![](img/383d1ef72f2613f3059f728c0a0ae780_32.png)

---

![](img/383d1ef72f2613f3059f728c0a0ae780_33.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_34.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_35.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_36.png)

## Series的索引与切片 🔍
Series的索引和切片操作与一维数组非常相似。

![](img/383d1ef72f2613f3059f728c0a0ae780_38.png)

*   **按位置索引**：使用中括号和隐式索引位置。
*   **按标签索引**：如果存在显示索引，可以使用中括号或点号（`.`）后接索引名。

```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])

![](img/383d1ef72f2613f3059f728c0a0ae780_40.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_41.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_42.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_43.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_44.png)

# 按位置索引
print(s[0])  # 输出: 100

![](img/383d1ef72f2613f3059f728c0a0ae780_45.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_46.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_48.png)

# 按标签索引
print(s[‘语文‘])  # 输出: 100
print(s.语文)     # 输出: 100 (注意：索引名需符合变量命名规则)

![](img/383d1ef72f2613f3059f728c0a0ae780_50.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_51.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_52.png)

# 切片
print(s[0:2]) # 输出前两个元素
```

---

![](img/383d1ef72f2613f3059f728c0a0ae780_54.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_55.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_56.png)

## Series的常用属性 📋
以下是Series对象一些常用的属性：

![](img/383d1ef72f2613f3059f728c0a0ae780_58.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_59.png)

```python
s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])

![](img/383d1ef72f2613f3059f728c0a0ae780_60.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_62.png)

print(s.shape)    # 形状，例如 (3,)
print(s.size)     # 元素个数，例如 3
print(s.index)    # 索引对象
print(s.values)   # 值构成的数组
print(s.dtype)    # 数据类型
```
**关于数据类型**：Series要求内部数据**类型统一**。如果创建时混用数字和字符串，Pandas会将其统一为`object`（对象）类型，通常意味着是字符串。

![](img/383d1ef72f2613f3059f728c0a0ae780_64.png)

---

![](img/383d1ef72f2613f3059f728c0a0ae780_65.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_66.png)

## Series的常用方法 ⚙️
Pandas为Series封装了许多实用的方法。

![](img/383d1ef72f2613f3059f728c0a0ae780_68.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_69.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_71.png)

以下是几个基础方法的使用：

![](img/383d1ef72f2613f3059f728c0a0ae780_73.png)

```python
s = Series(data=np.random.randint(60, 100, size=10))

![](img/383d1ef72f2613f3059f728c0a0ae780_74.png)

print(s.head())     # 默认显示前5个数据
print(s.head(3))    # 显示前3个数据
print(s.tail(3))    # 显示后3个数据
print(s.unique())   # 返回去重后的数组
```

![](img/383d1ef72f2613f3059f728c0a0ae780_76.png)

### 空值检测方法
在数据运算中可能产生空值（NaN），以下方法用于检测：

![](img/383d1ef72f2613f3059f728c0a0ae780_77.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_78.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_80.png)

```python
s1 = Series(data=[1, 2, 3], index=[‘A‘, ‘B‘, ‘C‘])
s2 = Series(data=[1, 2, 3], index=[‘A‘, ‘C‘, ‘D‘])

s_sum = s1 + s2 # Series相加
print(s_sum)
```
**输出示例：**
```
A    2.0
B    NaN
C    5.0
D    NaN
dtype: float64
```
**运算规则**：Series进行算术运算时，只有**索引一致**的元素才会参与计算，索引不一致的位置结果会用`NaN`（Not a Number）填充。

![](img/383d1ef72f2613f3059f728c0a0ae780_82.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_83.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_84.png)

```python
print(s_sum.isnull())   # 判断每个元素是否为空，返回布尔Series
print(s_sum.notnull())  # 判断每个元素是否非空，返回布尔Series
```

![](img/383d1ef72f2613f3059f728c0a0ae780_85.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_86.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_87.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_88.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_89.png)

### 算术运算方法
除了直接使用 `+`, `-`, `*`, `/` 运算符，也可以调用对应的方法，如 `add()`, `sub()`, `mul()`, `div()`。

![](img/383d1ef72f2613f3059f728c0a0ae780_90.png)

---

![](img/383d1ef72f2613f3059f728c0a0ae780_92.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_93.png)

## 总结 🎯
本节课我们一起学习了Pandas中的`Series`数据结构：
1.  **定位**：`Series`是一个类似一维数组的数据结构，用于存储一列数据，是构成`DataFrame`的基础。
2.  **创建**：可以通过列表、NumPy数组、字典等多种数据源创建，并能使用`index`参数指定有意义的显示索引。
3.  **操作**：支持类似数组的索引、切片操作，并拥有`shape`、`size`、`values`、`index`等常用属性。
4.  **方法**：掌握了`head()`、`tail()`、`unique()`、`isnull()`等常用方法。
5.  **运算**：理解了Series运算的核心规则——**按索引对齐**，索引不一致会导致结果中出现`NaN`。

![](img/383d1ef72f2613f3059f728c0a0ae780_94.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_95.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_96.png)

![](img/383d1ef72f2613f3059f728c0a0ae780_98.png)

`Series`作为一维数据的容器，逻辑相对简单。下一节，我们将学习由多个`Series`组成的二维表格结构——**DataFrame**，它是Pandas中功能更强大、使用更广泛的核心组件。