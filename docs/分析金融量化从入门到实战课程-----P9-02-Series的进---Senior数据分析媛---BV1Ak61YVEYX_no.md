# 数据分析+金融量化+数据清洗：P9：02 Series的进化之DataFrame

![](img/1942706539fa102d953fbb1d42c78d96_0.png)

![](img/1942706539fa102d953fbb1d42c78d96_2.png)

![](img/1942706539fa102d953fbb1d42c78d96_4.png)

## 📖 概述
在本节课中，我们将学习Pandas中一个核心的数据结构——**DataFrame**。我们将了解它是什么、如何创建它，以及它的一些基本属性。DataFrame是数据分析工作中最常用的工具之一，理解它是迈向数据分析实战的重要一步。

![](img/1942706539fa102d953fbb1d42c78d96_6.png)

## 🔍 什么是DataFrame？
在上一节中，我们介绍了Series，它是一个一维的数据结构。我们提到DataFrame是由Series组成的。那么，DataFrame应该是一个几维的数据结构呢？

![](img/1942706539fa102d953fbb1d42c78d96_8.png)

通过上节课的演示，我们看到一个Series表示一列数据，是一个一维结构。如果有多个Series组合在一起，就会形成多列。多列数据组合在一起，就形成了一个表格型的数据结构。

表格型的数据结构是二维的，因为它有行和列。因此，DataFrame本质上是一个**表格型的数据结构**。

![](img/1942706539fa102d953fbb1d42c78d96_10.png)

DataFrame的设计初衷是将Series的使用场景从一维拓展到二维。这意味着DataFrame包含两种不同形式的索引：**行索引**和**列索引**。因此，DataFrame由三个核心部分组成：
1.  行索引
2.  列索引
3.  行列索引所对应的值

![](img/1942706539fa102d953fbb1d42c78d96_12.png)

你可以把DataFrame完全理解为MySQL数据库中的一张表，它们都是表格型的数据结构，由行和列组成。

![](img/1942706539fa102d953fbb1d42c78d96_14.png)

![](img/1942706539fa102d953fbb1d42c78d96_16.png)

## 🛠️ 如何创建DataFrame？
了解了DataFrame是什么之后，我们来看看如何创建它。首先，我们需要从Pandas库中导入DataFrame类。

![](img/1942706539fa102d953fbb1d42c78d96_18.png)

![](img/1942706539fa102d953fbb1d42c78d96_20.png)

```python
from pandas import DataFrame
```

![](img/1942706539fa102d953fbb1d42c78d96_22.png)

接下来，我们介绍几种创建DataFrame的常用方法。

![](img/1942706539fa102d953fbb1d42c78d96_24.png)

### 方法一：使用二维列表
第一种方式是调用DataFrame的构造方法，并传入一个二维列表作为数据源。

![](img/1942706539fa102d953fbb1d42c78d96_26.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```
这段代码会创建一个两行三列的DataFrame。

### 方法二：使用NumPy数组
第二种方式是使用NumPy数组作为数据源。

![](img/1942706539fa102d953fbb1d42c78d96_28.png)

```python
import numpy as np
data = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data)
print(df2)
```
这段代码会创建一个六行四列的DataFrame，数据是0到100之间的随机整数。

![](img/1942706539fa102d953fbb1d42c78d96_30.png)

![](img/1942706539fa102d953fbb1d42c78d96_32.png)

![](img/1942706539fa102d953fbb1d42c78d96_34.png)

### 方法三：使用字典（最常用）
第三种，也是最灵活的方式，是使用字典作为数据源。字典的键（key）会自动成为DataFrame的列索引。

![](img/1942706539fa102d953fbb1d42c78d96_36.png)

![](img/1942706539fa102d953fbb1d42c78d96_38.png)

```python
dic = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```
此时，字典的键‘name‘和‘salary‘成为了DataFrame的列名。行索引默认是0, 1, 2（隐式索引）。

![](img/1942706539fa102d953fbb1d42c78d96_40.png)

![](img/1942706539fa102d953fbb1d42c78d96_42.png)

如果你想指定显示的行索引，可以在构造时使用`index`参数。

![](img/1942706539fa102d953fbb1d42c78d96_44.png)

![](img/1942706539fa102d953fbb1d42c78d96_46.png)

![](img/1942706539fa102d953fbb1d42c78d96_48.png)

```python
df3 = DataFrame(data=dic, index=[‘a‘, ‘b‘, ‘c‘])
print(df3)
```
现在，行索引变成了‘a‘, ‘b‘, ‘c‘，列索引是‘name‘和‘salary‘。

![](img/1942706539fa102d953fbb1d42c78d96_50.png)

## 📊 DataFrame的常用属性
创建好DataFrame后，我们可以查看它的一些常用属性。

以下是几个关键属性：
*   `df.values`: 返回DataFrame存储的二维数据，通常是一个NumPy数组。
*   `df.columns`: 返回DataFrame的列索引。
*   `df.index`: 返回DataFrame的行索引。
*   `df.shape`: 返回DataFrame的形状（行数，列数）。

![](img/1942706539fa102d953fbb1d42c78d96_52.png)

需要注意的是，**不能直接使用`df.dtype`查看整个DataFrame的数据类型**。因为DataFrame的不同列可以存储不同类型的数据（例如一列是字符串，一列是整数）。我们只能查看其中某一行或某一列（即一个Series）的数据类型。

![](img/1942706539fa102d953fbb1d42c78d96_54.png)

![](img/1942706539fa102d953fbb1d42c78d96_56.png)

## 💡 实战练习
现在，我们来完成一个练习：创建一个如下所示的DataFrame。

![](img/1942706539fa102d953fbb1d42c78d96_58.png)

![](img/1942706539fa102d953fbb1d42c78d96_60.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/1942706539fa102d953fbb1d42c78d96_62.png)

使用字典创建是最方便的方法。以下是创建代码：

![](img/1942706539fa102d953fbb1d42c78d96_64.png)

```python
# 定义数据字典
data_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}
# 创建DataFrame，并指定行索引
df_exercise = DataFrame(data=data_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

![](img/1942706539fa102d953fbb1d42c78d96_66.png)

## 📝 总结
本节课中，我们一起学习了Pandas的核心数据结构——DataFrame。
*   我们明确了DataFrame是一个**二维的表格型数据结构**，由行索引、列索引和值组成。
*   我们掌握了三种创建DataFrame的方法：**使用二维列表、NumPy数组和字典**，其中字典方式最为灵活便捷。
*   我们了解了DataFrame的几个基本属性：`values`、`columns`、`index`和`shape`。
*   最后，我们通过一个练习巩固了使用字典创建指定行列索引的DataFrame的技能。

![](img/1942706539fa102d953fbb1d42c78d96_68.png)

![](img/1942706539fa102d953fbb1d42c78d96_70.png)

![](img/1942706539fa102d953fbb1d42c78d96_72.png)

理解并熟练使用DataFrame，是进行后续数据操作和分析的基础。