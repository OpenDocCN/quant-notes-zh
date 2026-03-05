# Python金融量化：P9：02 Series的进化之DataFrame 📊

![](img/7748945b1156f72c7e698a9e4536111e_0.png)

在本节课中，我们将要学习Pandas库中的核心数据结构——DataFrame。我们将了解它是什么、如何创建它，以及它的一些基本属性。DataFrame是进行金融数据分析和处理的关键工具。

![](img/7748945b1156f72c7e698a9e4536111e_2.png)

![](img/7748945b1156f72c7e698a9e4536111e_4.png)

上一节我们介绍了Series，它是一种一维的数据结构。本节中我们来看看由Series组成的DataFrame。

![](img/7748945b1156f72c7e698a9e4536111e_6.png)

## DataFrame是什么？🤔

![](img/7748945b1156f72c7e698a9e4536111e_8.png)

通过上节课的演示，我们知道Series是一个一维的数据结构。如果由多个Series组成，就会有多列数据。多列数据组合在一起，就形成了一个表格型的数据结构。

表格型的数据结构是二维的，有行和列。因此，DataFrame是一个二维的表格型数据结构。它的设计初衷是将Series的使用场景从一维拓展到二维。

DataFrame由三个核心部分组成：
1.  **行索引**
2.  **列索引**
3.  行列索引所对应的**值**

![](img/7748945b1156f72c7e698a9e4536111e_10.png)

你可以把DataFrame完全理解为MySQL数据库中的一张表，它们都是表格型的数据结构。

## 如何创建DataFrame？🔨

![](img/7748945b1156f72c7e698a9e4536111e_12.png)

![](img/7748945b1156f72c7e698a9e4536111e_14.png)

知道了DataFrame是什么之后，我们来看看如何创建它。首先需要从pandas库中导入DataFrame类。

![](img/7748945b1156f72c7e698a9e4536111e_16.png)

```python
from pandas import DataFrame
```

![](img/7748945b1156f72c7e698a9e4536111e_18.png)

接下来，我们介绍几种创建DataFrame的方式。

![](img/7748945b1156f72c7e698a9e4536111e_20.png)

### 方式一：使用二维列表

![](img/7748945b1156f72c7e698a9e4536111e_22.png)

第一种方式是调用DataFrame的构造方法，并传入一个二维列表作为数据源。

![](img/7748945b1156f72c7e698a9e4536111e_24.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```

![](img/7748945b1156f72c7e698a9e4536111e_26.png)

这将创建一个两行三列的DataFrame。

### 方式二：使用NumPy数组

第二种方式是使用NumPy数组作为数据源。

![](img/7748945b1156f72c7e698a9e4536111e_28.png)

```python
import numpy as np
data = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data)
print(df2)
```

![](img/7748945b1156f72c7e698a9e4536111e_30.png)

![](img/7748945b1156f72c7e698a9e4536111e_32.png)

这将创建一个六行四列的DataFrame。

![](img/7748945b1156f72c7e698a9e4536111e_34.png)

### 方式三：使用字典（最常用）

![](img/7748945b1156f72c7e698a9e4536111e_36.png)

![](img/7748945b1156f72c7e698a9e4536111e_38.png)

第三种，也是最灵活的方式，是使用字典作为数据源。字典的键（key）会自动成为DataFrame的列索引。

![](img/7748945b1156f72c7e698a9e4536111e_40.png)

![](img/7748945b1156f72c7e698a9e4536111e_42.png)

```python
dic = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王老五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```

![](img/7748945b1156f72c7e698a9e4536111e_44.png)

![](img/7748945b1156f72c7e698a9e4536111e_46.png)

此时，行索引是默认的隐式索引（0, 1, 2）。我们可以通过`index`参数指定显示的行索引。

![](img/7748945b1156f72c7e698a9e4536111e_48.png)

```python
df3 = DataFrame(data=dic, index=[‘A‘, ‘B‘, ‘C‘])
print(df3)
```

![](img/7748945b1156f72c7e698a9e4536111e_50.png)

现在，DataFrame的行列索引都变成了显示的。

## DataFrame的常用属性 📋

创建好DataFrame后，我们可以查看它的一些常用属性。

![](img/7748945b1156f72c7e698a9e4536111e_52.png)

以下是DataFrame的几个关键属性及其含义：
*   **`df.values`**: 返回DataFrame存储的二维数据，通常是一个NumPy数组。
*   **`df.columns`**: 返回DataFrame的列索引。
*   **`df.index`**: 返回DataFrame的行索引。
*   **`df.shape`**: 返回DataFrame的形状，即（行数， 列数）。

![](img/7748945b1156f72c7e698a9e4536111e_54.png)

![](img/7748945b1156f72c7e698a9e4536111e_56.png)

需要注意的是，**不能直接使用`df.dtype`查看整个DataFrame的数据类型**。因为DataFrame的不同列可以存储不同类型的数据（例如一列是字符串，另一列是整数）。我们只能查看其中某一行或某一列（即一个Series）的数据类型。

![](img/7748945b1156f72c7e698a9e4536111e_58.png)

## 实践练习 ✍️

![](img/7748945b1156f72c7e698a9e4536111e_60.png)

![](img/7748945b1156f72c7e698a9e4536111e_62.png)

现在，我们来完成一个练习：创建一个如下所示的DataFrame。

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/7748945b1156f72c7e698a9e4536111e_64.png)

![](img/7748945b1156f72c7e698a9e4536111e_66.png)

使用字典创建是最便捷的方法。

```python
# 创建字典，键作为列名，值作为列数据
score_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}
# 创建DataFrame，并指定行索引
df_exercise = DataFrame(data=score_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

![](img/7748945b1156f72c7e698a9e4536111e_68.png)

![](img/7748945b1156f72c7e698a9e4536111e_70.png)

## 总结 📝

![](img/7748945b1156f72c7e698a9e4536111e_72.png)

本节课中我们一起学习了Pandas的二维数据结构DataFrame。
*   我们明确了**DataFrame是一个二维的表格型数据结构**，由行索引、列索引和值组成。
*   我们掌握了三种创建DataFrame的方法：**使用二维列表、NumPy数组和字典**，其中字典方式最为灵活。
*   我们了解了DataFrame的常用属性，如`.values`、`.columns`、`.index`和`.shape`。
*   我们通过一个练习巩固了使用字典创建指定行列索引的DataFrame的技能。

![](img/7748945b1156f72c7e698a9e4536111e_74.png)

![](img/7748945b1156f72c7e698a9e4536111e_76.png)

理解并熟练使用DataFrame是进行后续数据清洗、分析和可视化的基础。