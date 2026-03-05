# 数据分析之量化案例：P8：day02-02 Series的进化之DataFrame~1 📊

![](img/021c79d2a6498ae471645c055f19bfe6_0.png)

![](img/021c79d2a6498ae471645c055f19bfe6_2.png)

在本节课中，我们将要学习Pandas中一个核心的数据结构——DataFrame。我们将了解它是什么、如何创建它，以及它的一些基本属性。

![](img/021c79d2a6498ae471645c055f19bfe6_4.png)

![](img/021c79d2a6498ae471645c055f19bfe6_6.png)

## 什么是DataFrame？

![](img/021c79d2a6498ae471645c055f19bfe6_8.png)

在上一节中，我们介绍了Series，它是一个一维的数据结构。本节中我们来看看DataFrame。DataFrame是由Series组成的，那么它应该是一个几维的数据结构呢？

通过思考，我们可以得出结论：一个Series是一列数据，代表一维。多个Series组合在一起，就形成了多列，从而构成了一个表格型的数据结构。表格型的数据结构是二维的，因为它有行和列。

因此，DataFrame是一个**表格型的数据结构**。它的设计初衷是将Series的使用场景从一维拓展到二维。DataFrame由三个核心部分组成：
*   **行索引 (index)**
*   **列索引 (columns)**
*   行列索引所对应的**值 (values)**

![](img/021c79d2a6498ae471645c055f19bfe6_10.png)

你可以把DataFrame完全理解为MySQL数据库中的一张表，它们都是表格型的数据结构。

![](img/021c79d2a6498ae471645c055f19bfe6_12.png)

## 如何创建DataFrame？

![](img/021c79d2a6498ae471645c055f19bfe6_14.png)

![](img/021c79d2a6498ae471645c055f19bfe6_16.png)

知道了DataFrame是什么之后，我们来看看如何创建它。首先，我们需要从pandas库中导入DataFrame类。

![](img/021c79d2a6498ae471645c055f19bfe6_18.png)

![](img/021c79d2a6498ae471645c055f19bfe6_20.png)

```python
from pandas import DataFrame
```

![](img/021c79d2a6498ae471645c055f19bfe6_22.png)

以下是几种创建DataFrame的常用方法。

![](img/021c79d2a6498ae471645c055f19bfe6_24.png)

![](img/021c79d2a6498ae471645c055f19bfe6_26.png)

### 方法一：使用二维列表

![](img/021c79d2a6498ae471645c055f19bfe6_28.png)

我们可以直接调用DataFrame的构造方法，并传入一个二维列表作为数据源。

![](img/021c79d2a6498ae471645c055f19bfe6_30.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```
这段代码会创建一个两行三列的DataFrame。

### 方法二：使用NumPy数组

我们也可以使用NumPy生成的二维数组作为数据源。

![](img/021c79d2a6498ae471645c055f19bfe6_32.png)

![](img/021c79d2a6498ae471645c055f19bfe6_34.png)

![](img/021c79d2a6498ae471645c055f19bfe6_36.png)

```python
import numpy as np
data_np = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data_np)
print(df2)
```
这段代码会创建一个六行四列、数据为随机整数的DataFrame。

![](img/021c79d2a6498ae471645c055f19bfe6_38.png)

![](img/021c79d2a6498ae471645c055f19bfe6_40.png)

### 方法三：使用字典（最常用且灵活）

![](img/021c79d2a6498ae471645c055f19bfe6_42.png)

![](img/021c79d2a6498ae471645c055f19bfe6_44.png)

使用字典创建DataFrame非常便捷。字典的键（key）会自动成为DataFrame的列索引。

![](img/021c79d2a6498ae471645c055f19bfe6_46.png)

![](img/021c79d2a6498ae471645c055f19bfe6_48.png)

```python
data_dict = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王老五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=data_dict)
print(df3)
```
此时，行索引是默认的隐式索引（0, 1, 2）。我们还可以在创建时指定行索引。

![](img/021c79d2a6498ae471645c055f19bfe6_50.png)

![](img/021c79d2a6498ae471645c055f19bfe6_52.png)

```python
df3 = DataFrame(data=data_dict, index=[‘A‘, ‘B‘, ‘C‘])
print(df3)
```
现在，行索引和列索引都变成了显示索引。

![](img/021c79d2a6498ae471645c055f19bfe6_54.png)

## DataFrame的常用属性

创建好DataFrame后，我们可以查看它的一些基本属性。

![](img/021c79d2a6498ae471645c055f19bfe6_56.png)

*   **`df.values`**: 返回DataFrame中存储的二维数据，通常是一个NumPy数组。
*   **`df.columns`**: 返回列索引。
*   **`df.index`**: 返回行索引。
*   **`df.shape`**: 返回DataFrame的形状，即（行数， 列数）。

![](img/021c79d2a6498ae471645c055f19bfe6_58.png)

![](img/021c79d2a6498ae471645c055f19bfe6_60.png)

需要注意的是，**`df.dtype`** 这个属性在DataFrame上直接调用通常会报错。因为DataFrame的不同列可以存储不同类型的数据（例如一列是字符串，另一列是整数），所以无法用一个统一的类型来描述整个DataFrame。我们只能查看其中某一行或某一列（它们是一个Series）的数据类型。

![](img/021c79d2a6498ae471645c055f19bfe6_62.png)

![](img/021c79d2a6498ae471645c055f19bfe6_64.png)

## 练习：创建一个指定结构的DataFrame

![](img/021c79d2a6498ae471645c055f19bfe6_66.png)

现在，我们来完成一个练习：创建一个如下所示的DataFrame。

![](img/021c79d2a6498ae471645c055f19bfe6_68.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/021c79d2a6498ae471645c055f19bfe6_70.png)

![](img/021c79d2a6498ae471645c055f19bfe6_72.png)

根据我们学到的知识，使用字典创建是最方便的方法。

![](img/021c79d2a6498ae471645c055f19bfe6_74.png)

```python
exercise_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}
df_exercise = DataFrame(data=exercise_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

![](img/021c79d2a6498ae471645c055f19bfe6_76.png)

![](img/021c79d2a6498ae471645c055f19bfe6_78.png)

## 总结

![](img/021c79d2a6498ae471645c055f19bfe6_80.png)

![](img/021c79d2a6498ae471645c055f19bfe6_82.png)

本节课中我们一起学习了Pandas的二维数据结构DataFrame。我们了解到DataFrame是一个表格型数据结构，由行索引、列索引和值组成。我们掌握了三种创建DataFrame的方法：使用二维列表、NumPy数组和字典，其中字典方式最为灵活。我们还学习了DataFrame的几个基本属性：`values`、`columns`、`index`和`shape`。最后，我们通过一个练习巩固了如何使用字典创建指定结构的DataFrame。