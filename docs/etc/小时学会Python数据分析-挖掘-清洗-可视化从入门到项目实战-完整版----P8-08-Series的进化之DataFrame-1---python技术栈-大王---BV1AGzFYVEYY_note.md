# Python金融量化：P8：08 Series的进化之DataFrame 📊

![](img/ec134412b522ab19943fb50cde377147_0.png)

在本节课中，我们将要学习Pandas中另一个核心数据结构——DataFrame。我们将了解它是什么、如何创建以及其基本属性。

![](img/ec134412b522ab19943fb50cde377147_2.png)

![](img/ec134412b522ab19943fb50cde377147_4.png)

## 什么是DataFrame？ 🤔

![](img/ec134412b522ab19943fb50cde377147_6.png)

在上一节我们介绍了Series，它是一个一维的数据结构。本节中我们来看看DataFrame。

![](img/ec134412b522ab19943fb50cde377147_8.png)

DataFrame是由Series组成的。Series是一个一维的数据结构，而DataFrame是一个表格型的数据结构，因此它是二维的。这意味着DataFrame有行和列两种索引。

DataFrame的组成元素有三个：
1.  行索引
2.  列索引
3.  行列索引所对应的值

你可以把DataFrame完全理解为MySQL数据库中的一张表，它就是一个由行和列组成的表格。

![](img/ec134412b522ab19943fb50cde377147_10.png)

## 如何创建DataFrame？ 🛠️

![](img/ec134412b522ab19943fb50cde377147_12.png)

了解了DataFrame是什么之后，我们来看看如何创建它。首先需要导入DataFrame类。

![](img/ec134412b522ab19943fb50cde377147_14.png)

```python
from pandas import DataFrame
```

![](img/ec134412b522ab19943fb50cde377147_16.png)

![](img/ec134412b522ab19943fb50cde377147_18.png)

以下是几种创建DataFrame的常用方法。

![](img/ec134412b522ab19943fb50cde377147_20.png)

![](img/ec134412b522ab19943fb50cde377147_22.png)

### 方法一：使用二维列表

![](img/ec134412b522ab19943fb50cde377147_24.png)

调用DataFrame的构造方法，传入一个二维列表作为数据源。

![](img/ec134412b522ab19943fb50cde377147_26.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```

![](img/ec134412b522ab19943fb50cde377147_28.png)

这将创建一个两行三列的表格型数据结构。

![](img/ec134412b522ab19943fb50cde377147_30.png)

### 方法二：使用NumPy数组

数据源也可以是NumPy数组。

```python
import numpy as np
data = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data)
print(df2)
```

![](img/ec134412b522ab19943fb50cde377147_32.png)

![](img/ec134412b522ab19943fb50cde377147_34.png)

这将创建一个六行四列的表格。

![](img/ec134412b522ab19943fb50cde377147_36.png)

![](img/ec134412b522ab19943fb50cde377147_38.png)

### 方法三：使用字典（最常用且灵活）

![](img/ec134412b522ab19943fb50cde377147_40.png)

![](img/ec134412b522ab19943fb50cde377147_42.png)

使用字典创建DataFrame非常便捷，字典的键（key）会自动成为DataFrame的列索引。

![](img/ec134412b522ab19943fb50cde377147_44.png)

![](img/ec134412b522ab19943fb50cde377147_46.png)

```python
dic = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王老五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```

![](img/ec134412b522ab19943fb50cde377147_48.png)

![](img/ec134412b522ab19943fb50cde377147_50.png)

此时，行索引是默认的隐式索引（0, 1, 2）。如果你想指定显示的行索引，可以使用`index`参数。

![](img/ec134412b522ab19943fb50cde377147_52.png)

![](img/ec134412b522ab19943fb50cde377147_54.png)

```python
df3 = DataFrame(data=dic, index=[‘a‘, ‘b‘, ‘c‘])
print(df3)
```

## DataFrame的常用属性 📋

创建好DataFrame后，我们可以查看它的一些常用属性。

![](img/ec134412b522ab19943fb50cde377147_56.png)

以下是几个关键属性及其含义：
*   `df.values`: 返回DataFrame存储的二维数据，是一个NumPy数组。
*   `df.columns`: 返回列索引。
*   `df.index`: 返回行索引。
*   `df.shape`: 返回DataFrame的形状（行数，列数）。

![](img/ec134412b522ab19943fb50cde377147_58.png)

**注意**：不能直接使用`df.dtype`查看整个DataFrame的数据类型，因为不同列可以存储不同类型的数据（例如一列是字符串，另一列是整数）。只能单独查看某一行或某一列（它们返回的是Series）的数据类型。

![](img/ec134412b522ab19943fb50cde377147_60.png)

![](img/ec134412b522ab19943fb50cde377147_62.png)

## 实践练习 ✍️

![](img/ec134412b522ab19943fb50cde377147_64.png)

![](img/ec134412b522ab19943fb50cde377147_66.png)

现在，让我们通过一个练习来巩固所学知识。我们需要创建如下所示的DataFrame：

![](img/ec134412b522ab19943fb50cde377147_68.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/ec134412b522ab19943fb50cde377147_70.png)

使用字典创建是最方便的方法：

![](img/ec134412b522ab19943fb50cde377147_72.png)

```python
# 定义字典，键作为列名，值作为列数据
score_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}
# 创建DataFrame，并指定行索引
df_exercise = DataFrame(data=score_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

![](img/ec134412b522ab19943fb50cde377147_74.png)

![](img/ec134412b522ab19943fb50cde377147_76.png)

## 总结 📝

![](img/ec134412b522ab19943fb50cde377147_78.png)

本节课中我们一起学习了Pandas的核心数据结构DataFrame。
*   我们了解到DataFrame是一个二维的表格型数据结构，由行索引、列索引和值组成。
*   我们掌握了三种创建DataFrame的方法：使用二维列表、NumPy数组和字典，其中字典方式最为灵活。
*   我们熟悉了DataFrame的几个基本属性：`values`、`columns`、`index`和`shape`。
*   最后，我们通过一个练习巩固了使用字典创建指定行列索引的DataFrame的技能。

![](img/ec134412b522ab19943fb50cde377147_80.png)

![](img/ec134412b522ab19943fb50cde377147_82.png)

DataFrame是进行数据分析和操作的基础，在后续课程中我们将深入学习如何对DataFrame进行查询、筛选和计算。