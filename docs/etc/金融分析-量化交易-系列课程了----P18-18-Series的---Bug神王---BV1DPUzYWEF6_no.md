# Python金融分析+量化交易：P18：Series的进化之DataFrame

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_0.png)

在本节课中，我们将要学习Pandas库中另一个核心数据结构——DataFrame。我们将了解它是什么、如何创建它，以及它的一些基本属性。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_2.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_4.png)

## DataFrame是什么？📊

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_6.png)

上一节我们介绍了Series，它是一个一维的数据结构。本节中我们来看看DataFrame。DataFrame由Series组成，那么它应该是一个几维的数据结构呢？

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_8.png)

通过上节课的演示，我们知道一个Series表示一列数据，是一个一维结构。如果有多个Series组合在一起，就会形成多列。多列数据组合在一起，就形成了一个表格型的数据结构。

表格型的数据结构是二维的，因为它有行和列。因此，DataFrame本质上是一个**二维的表格型数据结构**。它的设计初衷是将Series的使用场景从一维拓展到二维。

DataFrame由三个核心部分组成：
*   **行索引 (index)**：标识每一行。
*   **列索引 (columns)**：标识每一列。
*   **值 (values)**：行列索引交叉点所对应的数据。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_10.png)

你可以把DataFrame完全理解为MySQL数据库中的一张表，它们都是具有行列结构的表格。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_12.png)

## 如何创建DataFrame？🔨

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_14.png)

知道了DataFrame是什么之后，我们再来看一下DataFrame怎么创建。首先需要从pandas库中导入它。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_16.png)

```python
from pandas import DataFrame
```

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_18.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_20.png)

接下来，我们调用DataFrame的构造方法进行创建。构造方法的主要参数有：
*   `data`：数据源，通常是二维数据。
*   `index`：行索引。
*   `columns`：列索引。
*   `dtype`：元素的数据类型。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_22.png)

以下是几种常见的创建方式：

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_24.png)

### 1. 使用二维列表创建

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_26.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```
这将创建一个两行三列的DataFrame。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_28.png)

### 2. 使用NumPy数组创建

```python
import numpy as np
data = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data)
print(df2)
```
这将创建一个六行四列的DataFrame，数据是0到100之间的随机整数。

### 3. 使用字典创建（最常用、最灵活）

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_30.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_32.png)

```python
dic = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王老五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```
使用字典创建时，**字典的键（key）会自动成为DataFrame的列索引**。此时行索引是默认的隐式索引（0, 1, 2...）。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_34.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_36.png)

如果你想指定显示的行索引，可以这样做：
```python
df3 = DataFrame(data=dic, index=[‘A‘, ‘B‘, ‘C‘])
print(df3)
```
现在，这个DataFrame的行列索引都变成了显示的。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_38.png)

## DataFrame的常用属性🔍

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_40.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_42.png)

创建好DataFrame后，我们可以查看它的一些常用属性。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_44.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_46.png)

```python
# 查看存储的二维数据，返回一个NumPy数组
print(df3.values)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_48.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_50.png)

# 查看列索引
print(df3.columns)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_52.png)

# 查看行索引
print(df3.index)

# 查看形状（行数，列数）
print(df3.shape)
```

需要注意的是，**不能直接使用`df.dtype`查看整个DataFrame的数据类型**。因为DataFrame的每一列可以存储不同类型的数据（例如一列是字符串，另一列是整数）。我们只能单独查看某一列或某一行的数据类型，因为取出的单列或单行是一个Series，而Series内部的数据类型是一致的。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_54.png)

## 实践练习✍️

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_56.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_58.png)

现在，我们来完成一个练习：创建一个如下所示的DataFrame。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_60.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_62.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_64.png)

根据我们学到的知识，使用字典创建是最方便的方法。

```python
# 定义数据字典，键作为列名
data_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_66.png)

# 创建DataFrame，并指定行索引
df_exercise = DataFrame(data=data_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_68.png)

## 总结📝

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_70.png)

本节课中我们一起学习了Pandas的核心数据结构DataFrame。
*   我们了解到DataFrame是一个**二维的表格型数据结构**，由行索引、列索引和值组成。
*   我们掌握了三种创建DataFrame的方法：**使用二维列表、NumPy数组和字典**，其中字典方式最为灵活便捷。
*   我们学习了DataFrame的几个基本属性：`.values`、`.columns`、`.index`和`.shape`。
*   我们还通过一个练习巩固了如何使用字典创建具有特定行列索引的DataFrame。

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_72.png)

![](img/da34c2417e5aa1f6d11ca9c20aa7ac74_74.png)

理解并熟练使用DataFrame是进行数据分析和处理的关键一步。