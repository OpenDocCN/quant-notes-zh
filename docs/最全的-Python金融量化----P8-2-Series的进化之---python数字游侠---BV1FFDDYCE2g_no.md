# Python金融量化与业务数据分析：P8：2. Series的进化之DataFrame

![](img/7a3b7d1a557398ddac1acf3d136b5559_0.png)

在本节课中，我们将要学习Pandas中另一个核心数据结构——DataFrame。上一节我们介绍了Series，它是一个一维的数据结构。本节中我们来看看如何将Series组合起来，形成功能更强大的二维表格型数据结构。

![](img/7a3b7d1a557398ddac1acf3d136b5559_2.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_4.png)

## DataFrame是什么？

![](img/7a3b7d1a557398ddac1acf3d136b5559_6.png)

通过上节课的演示，我们了解到Series是一个一维的数据结构。DataFrame由多个Series组成，如果有多列Series组合在一起，就形成了一个表格型的数据结构。表格型的数据结构是二维的，有行和列。

![](img/7a3b7d1a557398ddac1acf3d136b5559_8.png)

因此，DataFrame是一个表格型的数据结构。它的设计初衷是将Series的使用场景从一维拓展到二维。Series只有一种索引，而DataFrame是二维的，所以它有两种不同形式的索引，分别是行索引和列索引。

DataFrame的组成元素有三个：
1.  行索引
2.  列索引
3.  行列索引所对应的值

你可以把DataFrame完全当成MySQL数据库中的一张表，它就是一个由行和列组成的表格。

![](img/7a3b7d1a557398ddac1acf3d136b5559_10.png)

## 如何创建DataFrame？

知道了DataFrame是什么之后，我们再来看一下DataFrame怎么创建。首先需要从pandas中导入DataFrame类。

![](img/7a3b7d1a557398ddac1acf3d136b5559_12.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_14.png)

```python
from pandas import DataFrame
```

![](img/7a3b7d1a557398ddac1acf3d136b5559_16.png)

接下来，我们介绍几种创建DataFrame的方式。

![](img/7a3b7d1a557398ddac1acf3d136b5559_18.png)

### 方式一：使用二维列表

![](img/7a3b7d1a557398ddac1acf3d136b5559_20.png)

第一种方式是调用DataFrame的构造方法进行创建。构造方法的主要参数有：
*   `data`：数据源，通常是二维数据。
*   `index`：行索引。
*   `columns`：列索引。
*   `dtype`：元素类型。

![](img/7a3b7d1a557398ddac1acf3d136b5559_22.png)

以下是使用二维列表作为数据源的例子：

![](img/7a3b7d1a557398ddac1acf3d136b5559_24.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```

![](img/7a3b7d1a557398ddac1acf3d136b5559_26.png)

这将创建一个两行三列的DataFrame。

### 方式二：使用NumPy数组

第二种方式是使用NumPy数组作为数据源。

![](img/7a3b7d1a557398ddac1acf3d136b5559_28.png)

```python
import numpy as np
data_np = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data_np)
print(df2)
```

![](img/7a3b7d1a557398ddac1acf3d136b5559_30.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_32.png)

这将创建一个六行四列的DataFrame，数据是随机生成的整数。

![](img/7a3b7d1a557398ddac1acf3d136b5559_34.png)

### 方式三：使用字典（最常用）

![](img/7a3b7d1a557398ddac1acf3d136b5559_36.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_38.png)

第三种，也是最灵活便捷的方式，是使用字典作为数据源。

![](img/7a3b7d1a557398ddac1acf3d136b5559_40.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_42.png)

```python
dic = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王老五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```

![](img/7a3b7d1a557398ddac1acf3d136b5559_44.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_46.png)

使用字典创建DataFrame时，字典的键（key）会自动成为DataFrame的列索引。此时，行索引默认是隐式索引（0, 1, 2...）。

![](img/7a3b7d1a557398ddac1acf3d136b5559_48.png)

如果你想指定显示的行索引和列索引，可以在构造时传入参数：

![](img/7a3b7d1a557398ddac1acf3d136b5559_50.png)

```python
df3 = DataFrame(data=dic, index=[‘A‘, ‘B‘, ‘C‘])
print(df3)
```

## DataFrame的常用属性

创建好DataFrame后，我们来了解几个常用的属性。

![](img/7a3b7d1a557398ddac1acf3d136b5559_52.png)

*   `df.values`：返回DataFrame存储的二维数据，通常是一个NumPy数组。
*   `df.columns`：返回列索引。
*   `df.index`：返回行索引。
*   `df.shape`：返回DataFrame的形状（行数，列数）。

![](img/7a3b7d1a557398ddac1acf3d136b5559_54.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_56.png)

需要注意的是，**不能直接使用`df.dtype`查看整个DataFrame的数据类型**。因为DataFrame的每一列可以存储不同类型的数据（例如一列是字符串，另一列是整数）。只能取出其中的某一行或某一列（它们是一个Series）后，再查看其数据类型。

![](img/7a3b7d1a557398ddac1acf3d136b5559_58.png)

## 练习：创建一个指定结构的DataFrame

![](img/7a3b7d1a557398ddac1acf3d136b5559_60.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_62.png)

现在，我们通过一个练习来巩固所学内容。我们需要创建一个如下所示的DataFrame，它表示两位同学在不同科目上的成绩：

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/7a3b7d1a557398ddac1acf3d136b5559_64.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_66.png)

根据我们学到的知识，使用字典创建最为方便。

```python
# 创建数据字典，键作为列名，值作为该列的数据
score_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}

![](img/7a3b7d1a557398ddac1acf3d136b5559_68.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_70.png)

# 创建DataFrame，并指定行索引（科目）
df_score = DataFrame(data=score_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_score)
```

![](img/7a3b7d1a557398ddac1acf3d136b5559_72.png)

## 总结

![](img/7a3b7d1a557398ddac1acf3d136b5559_74.png)

![](img/7a3b7d1a557398ddac1acf3d136b5559_76.png)

本节课中我们一起学习了Pandas的核心数据结构DataFrame。我们首先明确了DataFrame是一个二维的表格型数据结构，由行索引、列索引和对应的数据值组成。然后，我们掌握了三种创建DataFrame的方法：使用二维列表、NumPy数组以及最常用的字典。我们还了解了DataFrame的几个基本属性，如`.values`、`.columns`、`.index`和`.shape`。最后，通过一个创建成绩表的练习，我们巩固了使用字典灵活创建DataFrame的技巧。