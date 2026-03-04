# 数据分析实战：Pandas入门：07 Series数据结构详解 📊

![](img/9a76a820aa4aec826e03318c87826fae_1.png)

在本节课中，我们将要学习Pandas模块中的第一个核心数据结构——Series。我们将了解它是什么、为什么需要它、如何创建它以及如何使用它进行基本的数据操作。

![](img/9a76a820aa4aec826e03318c87826fae_3.png)

## 课程概述

上一节我们回顾了NumPy模块，它主要用于处理数值型数据。本节中，我们来看看Pandas模块，它被设计用来处理非数值型数据（如字符串），是数据分析中不可或缺的工具。

![](img/9a76a820aa4aec826e03318c87826fae_5.png)

## 为什么需要Pandas？🤔

![](img/9a76a820aa4aec826e03318c87826fae_7.png)

NumPy模块可以很好地存储和处理数值型数据。然而，在数据分析中，我们经常需要处理大量的非数值型数据，例如字符串。Pandas模块就是为了高效处理这类数据而设计的。虽然Pandas也能处理数值型数据，但其主要优势在于处理非数值型数据。

![](img/9a76a820aa4aec826e03318c87826fae_9.png)

## Pandas中的核心数据结构

![](img/9a76a820aa4aec826e03318c87826fae_11.png)

在Pandas模块中，我们需要重点学习两个常用的类，它们也是两种核心的数据结构：
*   **Series**
*   **DataFrame**

![](img/9a76a820aa4aec826e03318c87826fae_13.png)

数据结构可以理解为一种容器，用于装载和存储数据。Pandas中的Series和DataFrame就是这样的容器，它们封装了丰富的工具和方法，方便我们对存储的数据进行处理。

![](img/9a76a820aa4aec826e03318c87826fae_15.png)

![](img/9a76a820aa4aec826e03318c87826fae_17.png)

**注意**：DataFrame是Pandas中更常用、功能更强大的数据结构，而Series是理解DataFrame的基础，因为DataFrame本质上是由多个Series组成的。

## 什么是Series？📈

Series是一种一维的数据结构。我们可以暂且将其理解为一个一维数组，但它与NumPy数组有所不同。一个Series对象由两部分组成：
*   **`values`**：存储的数据元素。
*   **`index`**：每个数据元素对应的索引。

![](img/9a76a820aa4aec826e03318c87826fae_19.png)

其结构可以简单表示为：`Series = 索引(index) + 值(values)`

![](img/9a76a820aa4aec826e03318c87826fae_21.png)

![](img/9a76a820aa4aec826e03318c87826fae_23.png)

![](img/9a76a820aa4aec826e03318c87826fae_25.png)

## 创建Series

![](img/9a76a820aa4aec826e03318c87826fae_27.png)

首先，我们需要导入Series类。

![](img/9a76a820aa4aec826e03318c87826fae_29.png)

```python
from pandas import Series
```

![](img/9a76a820aa4aec826e03318c87826fae_31.png)

导入后，我们就可以使用Series的构造函数来创建Series对象了。构造函数常用的参数有：
*   `data`：数据源。
*   `index`：索引（可选，不指定则使用默认的隐式索引0,1,2…）。
*   `dtype`：数据类型（可选）。

![](img/9a76a820aa4aec826e03318c87826fae_33.png)

以下是几种常见的数据源创建方式：

![](img/9a76a820aa4aec826e03318c87826fae_35.png)

![](img/9a76a820aa4aec826e03318c87826fae_37.png)

![](img/9a76a820aa4aec826e03318c87826fae_39.png)

**1. 使用列表创建**

![](img/9a76a820aa4aec826e03318c87826fae_41.png)

![](img/9a76a820aa4aec826e03318c87826fae_43.png)

![](img/9a76a820aa4aec826e03318c87826fae_45.png)

```python
s = Series(data=[1, 2, 3, 4])
print(s)
```
输出结果中，左侧是索引（默认为0,1,2,3），右侧是值。

**2. 使用NumPy数组创建**
Series是一维结构，因此数据源也必须是一维的。
```python
import numpy as np
data = np.random.randint(0, 100, size=5) # 创建一维数组
s = Series(data=data)
print(s)
```

![](img/9a76a820aa4aec826e03318c87826fae_47.png)

![](img/9a76a820aa4aec826e03318c87826fae_49.png)

![](img/9a76a820aa4aec826e03318c87826fae_51.png)

**3. 使用字典创建**
使用字典创建时，字典的键（key）会自动成为Series的显示索引。
```python
data = {‘语文‘: 100, ‘数学‘: 99, ‘理综‘: 250}
s = Series(data=data)
print(s)
```

![](img/9a76a820aa4aec826e03318c87826fae_53.png)

![](img/9a76a820aa4aec826e03318c87826fae_55.png)

![](img/9a76a820aa4aec826e03318c87826fae_57.png)

**4. 指定显示索引**
我们可以通过`index`参数自定义索引，这能大大增强数据的可读性。
```python
s = Series(data=[1, 2, 3, 4], index=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(s)
```

![](img/9a76a820aa4aec826e03318c87826fae_59.png)

![](img/9a76a820aa4aec826e03318c87826fae_61.png)

![](img/9a76a820aa4aec826e03318c87826fae_63.png)

## Series的索引与切片

![](img/9a76a820aa4aec826e03318c87826fae_65.png)

![](img/9a76a820aa4aec826e03318c87826fae_67.png)

![](img/9a76a820aa4aec826e03318c87826fae_69.png)

Series的索引和切片操作与一维数组非常相似。

![](img/9a76a820aa4aec826e03318c87826fae_71.png)

*   **索引**：可以通过位置（隐式索引）或自定义的显示索引来访问元素。
    ```python
    s = Series(data=[100, 99, 250], index=[‘语文‘, ‘数学‘, ‘理综‘])
    print(s[0]) # 通过隐式索引访问，输出：100
    print(s[‘语文‘]) # 通过显示索引访问，输出：100
    print(s.语文) # 通过属性方式访问显示索引（仅当索引是有效的Python标识符时），输出：100
    ```
*   **切片**：与列表切片规则一致。
    ```python
    print(s[0:2]) # 切片，输出前两个元素
    ```

![](img/9a76a820aa4aec826e03318c87826fae_73.png)

![](img/9a76a820aa4aec826e03318c87826fae_75.png)

## Series的常用属性

![](img/9a76a820aa4aec826e03318c87826fae_77.png)

![](img/9a76a820aa4aec826e03318c87826fae_79.png)

![](img/9a76a820aa4aec826e03318c87826fae_81.png)

创建Series后，我们可以通过以下属性获取其基本信息：

![](img/9a76a820aa4aec826e03318c87826fae_83.png)

```python
s = Series(data=[1, 2, 3, 4], index=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(s.shape)   # 形状，输出：(4,)
print(s.size)    # 元素个数，输出：4
print(s.index)   # 索引，输出：Index([‘A‘, ‘B‘, ‘C‘, ‘D‘], dtype=‘object‘)
print(s.values)  # 值数组，输出：[1 2 3 4]
print(s.dtype)   # 数据类型，输出：int64
```
**注意**：Series要求内部数据的类型是统一的。如果创建时混用类型（如整数和字符串），Pandas会尝试进行类型转换（通常转为`object`，即字符串类型）。

![](img/9a76a820aa4aec826e03318c87826fae_85.png)

![](img/9a76a820aa4aec826e03318c87826fae_87.png)

## Series的常用方法

![](img/9a76a820aa4aec826e03318c87826fae_89.png)

![](img/9a76a820aa4aec826e03318c87826fae_91.png)

![](img/9a76a820aa4aec826e03318c87826fae_93.png)

Series对象提供了许多便捷的方法来处理数据。

![](img/9a76a820aa4aec826e03318c87826fae_95.png)

```python
import numpy as np
s = Series(data=np.random.randint(60, 100, size=10))

![](img/9a76a820aa4aec826e03318c87826fae_97.png)

![](img/9a76a820aa4aec826e03318c87826fae_99.png)

print(s.head(3))   # 查看前3条数据
print(s.tail(3))   # 查看后3条数据
print(s.unique())  # 返回去重后的数组
print(s.isnull())  # 检查每个元素是否为空值（NaN），返回布尔Series
print(s.notnull()) # 检查每个元素是否为非空值，返回布尔Series
```

## Series的运算规则

![](img/9a76a820aa4aec826e03318c87826fae_101.png)

![](img/9a76a820aa4aec826e03318c87826fae_103.png)

![](img/9a76a820aa4aec826e03318c87826fae_105.png)

两个Series进行算术运算（如加法）时，遵循一个核心规则：**只有索引相同的元素才会进行运算**。

![](img/9a76a820aa4aec826e03318c87826fae_107.png)

![](img/9a76a820aa4aec826e03318c87826fae_109.png)

![](img/9a76a820aa4aec826e03318c87826fae_111.png)

```python
s1 = Series(data=[1, 2, 3], index=[‘A‘, ‘B‘, ‘C‘])
s2 = Series(data=[1, 2, 3], index=[‘A‘, ‘C‘, ‘D‘])

![](img/9a76a820aa4aec826e03318c87826fae_113.png)

![](img/9a76a820aa4aec826e03318c87826fae_115.png)

![](img/9a76a820aa4aec826e03318c87826fae_117.png)

s = s1 + s2
print(s)
```
输出结果：
```
A    2.0
B    NaN
C    5.0
D    NaN
dtype: float64
```
*   索引`A`和`C`在两个Series中都存在，因此值相加（1+1=2， 3+2=5）。
*   索引`B`只在`s1`中存在，索引`D`只在`s2`中存在，由于找不到对应的运算元素，结果用空值`NaN`填充。

![](img/9a76a820aa4aec826e03318c87826fae_119.png)

![](img/9a76a820aa4aec826e03318c87826fae_121.png)

![](img/9a76a820aa4aec826e03318c87826fae_123.png)

`isnull()`和`notnull()`方法常用来检测此类运算后产生的空值。

![](img/9a76a820aa4aec826e03318c87826fae_125.png)

![](img/9a76a820aa4aec826e03318c87826fae_127.png)

![](img/9a76a820aa4aec826e03318c87826fae_129.png)

## 课程总结

![](img/9a76a820aa4aec826e03318c87826fae_131.png)

![](img/9a76a820aa4aec826e03318c87826fae_133.png)

![](img/9a76a820aa4aec826e03318c87826fae_135.png)

本节课中我们一起学习了Pandas的基础数据结构——Series。
*   我们了解了Series是一个**一维**的、带索引的数据容器，常用于存储一列数据。
*   我们掌握了使用**列表、NumPy数组、字典**等多种方式创建Series，并学会了通过`index`参数设置**显示索引**以增强可读性。
*   我们学习了Series的**基本操作**，包括索引、切片、查看属性和调用常用方法（如`head`, `tail`, `unique`）。
*   我们理解了Series运算的**核心规则**：索引对齐。这为后续学习更复杂的DataFrame打下了坚实基础。

![](img/9a76a820aa4aec826e03318c87826fae_137.png)

Series是构成DataFrame的基石。下一节，我们将进入Pandas的核心——DataFrame的学习，看看如何将多个Series组合成一个二维表格，并进行更强大的数据分析。