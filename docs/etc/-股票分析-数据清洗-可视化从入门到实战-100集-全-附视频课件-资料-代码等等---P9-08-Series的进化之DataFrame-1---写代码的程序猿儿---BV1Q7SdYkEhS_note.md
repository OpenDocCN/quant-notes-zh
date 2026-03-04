# Python金融量化：P9：08 Series的进化之DataFrame 📊

![](img/de2faa8440b2969558d583b668afc19f_0.png)

在本节课中，我们将要学习Pandas库中另一个核心数据结构——DataFrame。我们将了解它是什么、如何创建它，以及它的一些基本属性。

![](img/de2faa8440b2969558d583b668afc19f_2.png)

上一节我们介绍了Series，它是一种一维的数据结构。本节中我们来看看由Series组成的DataFrame。

![](img/de2faa8440b2969558d583b668afc19f_4.png)

## DataFrame是什么？ 🤔

![](img/de2faa8440b2969558d583b668afc19f_6.png)

通过上节课的演示，我们创建了一个DataFrame，它显示为一列数据。这一列表示一个一维的数据结构。如果有多个Series组成，就会有多列。多列组合在一起，就形成了一个表格型的数据结构。

![](img/de2faa8440b2969558d583b668afc19f_8.png)

表格型的数据结构是二维的，有行和列。因此，DataFrame是一个表格型的数据结构。DataFrame的设计初衷是将Series的使用场景从一维拓展到二维。

Series是一维的，只有一个索引。DataFrame是二维的，因此有两种不同形式的索引，分别是行索引和列索引。这意味着DataFrame由三个部分组成：行索引、列索引以及行列索引所对应的值。

你可以把DataFrame完全当成MySQL数据库中的一张表。MySQL的库表就是表格型的数据结构，由行和列组成，也有行列索引。DataFrame就是一个表格型的数据结构。

## 如何创建DataFrame？ 🛠️

![](img/de2faa8440b2969558d583b668afc19f_10.png)

知道了DataFrame是什么之后，我们来看一下如何创建它。首先需要导入DataFrame类。

```python
from pandas import DataFrame
```

![](img/de2faa8440b2969558d583b668afc19f_12.png)

接下来，我们介绍几种创建DataFrame的方式。

![](img/de2faa8440b2969558d583b668afc19f_14.png)

### 使用二维列表创建

![](img/de2faa8440b2969558d583b668afc19f_16.png)

第一种方式是调用DataFrame的构造方法，使用二维列表作为数据源。

![](img/de2faa8440b2969558d583b668afc19f_18.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```

![](img/de2faa8440b2969558d583b668afc19f_20.png)

这将创建一个两行三列的表格型数据结构。

![](img/de2faa8440b2969558d583b668afc19f_22.png)

### 使用NumPy数组创建

第二种方式是使用NumPy数组充当数据。

![](img/de2faa8440b2969558d583b668afc19f_24.png)

```python
import numpy as np
data_np = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data_np)
print(df2)
```

这将返回一个六行四列所对应的表格型数据结构。

### 使用字典创建

第三种方式是使用字典充当数据。这种方式更加灵活。

![](img/de2faa8440b2969558d583b668afc19f_26.png)

![](img/de2faa8440b2969558d583b668afc19f_28.png)

```python
dic = {
    'name': ['张三', '李四', '王老五'],
    'salary': [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```

![](img/de2faa8440b2969558d583b668afc19f_30.png)

使用字典创建DataFrame时，字典的key会成为DataFrame的列索引。此时行索引默认是隐式索引（0, 1, 2...）。如果你想指定行索引，可以使用`index`参数。

![](img/de2faa8440b2969558d583b668afc19f_32.png)

```python
df3 = DataFrame(data=dic, index=['A', 'B', 'C'])
print(df3)
```

![](img/de2faa8440b2969558d583b668afc19f_34.png)

![](img/de2faa8440b2969558d583b668afc19f_36.png)

现在，这个DataFrame的行列索引都变成了显示的。

![](img/de2faa8440b2969558d583b668afc19f_38.png)

通过以上三种方式，我们就可以创建出二维的表格形式的数据结构——DataFrame。

![](img/de2faa8440b2969558d583b668afc19f_40.png)

![](img/de2faa8440b2969558d583b668afc19f_42.png)

## DataFrame的常用属性 📋

![](img/de2faa8440b2969558d583b668afc19f_44.png)

以下是DataFrame的一些常用属性。

![](img/de2faa8440b2969558d583b668afc19f_46.png)

![](img/de2faa8440b2969558d583b668afc19f_48.png)

```python
# 返回DataFrame存储的二维形式的数据，是一个二维的NumPy数组
print(df.values)

# 返回列索引
print(df.columns)

# 返回行索引
print(df.index)

# 返回形状（行数，列数）
print(df.shape)
```

![](img/de2faa8440b2969558d583b668afc19f_50.png)

需要注意的是，不能直接使用`df.dtype`查看整个DataFrame的数据类型，因为DataFrame的不同列可以存储不同类型的数据（例如一列是字符串，另一列是整数）。你只能查看DataFrame中某一行或某一列（即一个Series）的数据类型。

![](img/de2faa8440b2969558d583b668afc19f_52.png)

## 练习：创建一个指定结构的DataFrame 📝

![](img/de2faa8440b2969558d583b668afc19f_54.png)

现在，我们来看一个练习：需要创建一个如下形式的DataFrame，它由四行两列组成。

![](img/de2faa8440b2969558d583b668afc19f_56.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/de2faa8440b2969558d583b668afc19f_58.png)

![](img/de2faa8440b2969558d583b668afc19f_60.png)

使用字典作为数据源创建这个DataFrame最为方便。

```python
# 创建字典，key将成为列索引
data_dict = {
    '张三': [150, 150, 150, 150],
    '李四': [0, 0, 0, 0]
}
# 创建DataFrame，并指定行索引
df_exercise = DataFrame(data=data_dict, index=['语文', '数学', '英语', '理综'])
print(df_exercise)
```

这样就创建好了与要求一模一样的DataFrame。使用字典创建DataFrame更加灵活和便捷。如果使用NumPy数组作为数据，还需要手动声明显示的行索引和列索引。

## 总结 🎯

![](img/de2faa8440b2969558d583b668afc19f_62.png)

本节课中我们一起学习了Pandas的核心数据结构DataFrame。

![](img/de2faa8440b2969558d583b668afc19f_64.png)

![](img/de2faa8440b2969558d583b668afc19f_66.png)

*   我们了解到DataFrame是一个**二维的表格型数据结构**，由行索引、列索引和对应的值组成。
*   我们掌握了三种创建DataFrame的方法：使用**二维列表**、**NumPy数组**和**字典**，其中字典方式最为灵活。
*   我们学习了DataFrame的几个基本属性：`.values`、`.columns`、`.index`和`.shape`。
*   我们通过一个练习巩固了使用字典创建指定结构DataFrame的技能。

![](img/de2faa8440b2969558d583b668afc19f_68.png)

DataFrame是进行数据分析和处理的基础，在后续的课程中我们将深入学习如何操作和分析DataFrame中的数据。