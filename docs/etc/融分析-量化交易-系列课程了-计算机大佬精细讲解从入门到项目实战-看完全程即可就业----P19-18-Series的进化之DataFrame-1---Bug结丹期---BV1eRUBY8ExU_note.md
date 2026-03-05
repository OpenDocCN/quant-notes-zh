# Python金融分析+量化交易：P19：DataFrame的基本使用

![](img/19fb4dd044ccaf9520267a847011a530_0.png)

![](img/19fb4dd044ccaf9520267a847011a530_2.png)

在本节课中，我们将要学习Pandas库中另一个核心数据结构——DataFrame。我们将了解它是什么、如何创建它，以及它的一些基本属性。

![](img/19fb4dd044ccaf9520267a847011a530_4.png)

## DataFrame是什么？📊

![](img/19fb4dd044ccaf9520267a847011a530_6.png)

![](img/19fb4dd044ccaf9520267a847011a530_8.png)

上一节我们介绍了Series，它是一个一维的数据结构。本节中我们来看看DataFrame。DataFrame由Series组成，那么它应该是一个几维的数据结构呢？

通过上节课的演示，我们如果创建了一个DataFrame，它显示为一列数据，这表示一个一维的数据结构。如果有多个Series组成，就会有多列。多列组合在一起，就形成了一个表格型的数据结构。

表格型的数据结构是二维的，有行和列。因此，DataFrame是一个表格型的数据结构。它的设计初衷是将Series的使用场景从一维拓展到二维。

![](img/19fb4dd044ccaf9520267a847011a530_10.png)

DataFrame的组成元素有三个：
*   第一个是行索引。
*   第二个是列索引。
*   第三个是行列索引所对应的值。

你可以把DataFrame完全当成MySQL数据库中的一张表，它就是一个由行和列组成的表格型数据结构。

![](img/19fb4dd044ccaf9520267a847011a530_12.png)

![](img/19fb4dd044ccaf9520267a847011a530_14.png)

## 如何创建DataFrame？🔨

![](img/19fb4dd044ccaf9520267a847011a530_16.png)

知道了DataFrame是什么之后，我们再来看一下DataFrame怎么创建。首先需要导入DataFrame类。

![](img/19fb4dd044ccaf9520267a847011a530_18.png)

![](img/19fb4dd044ccaf9520267a847011a530_20.png)

```python
from pandas import DataFrame
```

![](img/19fb4dd044ccaf9520267a847011a530_22.png)

接下来，我们调用DataFrame的构造方法进行创建。构造方法的主要参数有：
*   `data`：数据源，是二维的数据。
*   `index`：行索引。
*   `columns`：列索引。
*   `dtype`：元素类型。

![](img/19fb4dd044ccaf9520267a847011a530_24.png)

![](img/19fb4dd044ccaf9520267a847011a530_26.png)

以下是几种常见的创建方式：

![](img/19fb4dd044ccaf9520267a847011a530_28.png)

**1. 使用二维列表创建**

![](img/19fb4dd044ccaf9520267a847011a530_30.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```
这将返回一个两行三列的表格型数据结构。

**2. 使用NumPy数组创建**

```python
import numpy as np
data_np = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data_np)
print(df2)
```
这将返回一个六行四列的表格型数据结构。

![](img/19fb4dd044ccaf9520267a847011a530_32.png)

![](img/19fb4dd044ccaf9520267a847011a530_34.png)

**3. 使用字典创建**

![](img/19fb4dd044ccaf9520267a847011a530_36.png)

![](img/19fb4dd044ccaf9520267a847011a530_38.png)

```python
dic = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王老五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=dic)
print(df3)
```
使用字典创建DataFrame时，字典的key会成为DataFrame的列索引。此时行索引默认是隐式索引（0, 1, 2...）。

![](img/19fb4dd044ccaf9520267a847011a530_40.png)

![](img/19fb4dd044ccaf9520267a847011a530_42.png)

如果想指定显示的行索引，可以这样做：
```python
df3 = DataFrame(data=dic, index=[‘A‘, ‘B‘, ‘C‘])
print(df3)
```
现在，这个DataFrame的行列索引都变成显示的了。

![](img/19fb4dd044ccaf9520267a847011a530_44.png)

![](img/19fb4dd044ccaf9520267a847011a530_46.png)

通过以上三种方式，我们就可以创建出二维的表格形式的数据结构——DataFrame。

![](img/19fb4dd044ccaf9520267a847011a530_48.png)

![](img/19fb4dd044ccaf9520267a847011a530_50.png)

## DataFrame的常用属性🔧

![](img/19fb4dd044ccaf9520267a847011a530_52.png)

![](img/19fb4dd044ccaf9520267a847011a530_54.png)

下面看一下DataFrame常用的属性，这些属性很简单。

```python
# 假设 df 是我们之前用字典创建的 DataFrame (df3)
print(df.values)   # 返回DataFrame存储的二维形式的数据，是一个二维的NumPy数组。
print(df.columns)  # 返回列索引。
print(df.index)    # 返回行索引。
print(df.shape)    # 返回形状，例如 (3, 2) 表示三行两列。
```

需要注意的是，不能直接使用 `df.dtype` 查看整个DataFrame的数据类型。因为DataFrame的每一列可以存储不同类型的数据（例如一列是字符串，一列是整数）。只能取出其中的某一行或某一列（它们返回的是Series）来查看其数据类型。

![](img/19fb4dd044ccaf9520267a847011a530_56.png)

## 练习：创建一个指定格式的DataFrame📝

![](img/19fb4dd044ccaf9520267a847011a530_58.png)

![](img/19fb4dd044ccaf9520267a847011a530_60.png)

现在，我们来看一个练习。需要创建一个如下形式的DataFrame，它由四行两列组成：

![](img/19fb4dd044ccaf9520267a847011a530_62.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/19fb4dd044ccaf9520267a847011a530_64.png)

![](img/19fb4dd044ccaf9520267a847011a530_66.png)

这个怎么创建呢？使用字典作为数据源比较方便，因为字典的key会成为DataFrame的列索引。

![](img/19fb4dd044ccaf9520267a847011a530_68.png)

```python
# 创建字典，key作为列名，value作为对应列的数据
dic = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}

![](img/19fb4dd044ccaf9520267a847011a530_70.png)

# 使用DataFrame构造方法，并指定行索引
df_exercise = DataFrame(data=dic, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```
这样就创建好了与要求一模一样的DataFrame。使用字典创建DataFrame更加灵活和便捷。如果使用NumPy数组作为数据，还需要手动声明显示的行索引和列索引。

![](img/19fb4dd044ccaf9520267a847011a530_72.png)

![](img/19fb4dd044ccaf9520267a847011a530_74.png)

## 总结📚

![](img/19fb4dd044ccaf9520267a847011a530_76.png)

本节课中我们一起学习了DataFrame的基本知识：
1.  **DataFrame是什么**：它是一个二维的、表格型的数据结构，由行索引、列索引和对应的值组成，可以看作是Series从一维到二维的拓展。
2.  **如何创建DataFrame**：我们学习了三种主要方式——使用二维列表、NumPy数组和字典。其中，使用字典创建最为便捷，字典的key会自动成为列索引。
3.  **DataFrame的常用属性**：我们了解了`.values`、`.columns`、`.index`和`.shape`等属性的含义和用法。
4.  **注意事项**：DataFrame整体没有单一的`dtype`属性，因为不同列可以存储不同类型的数据。

![](img/19fb4dd044ccaf9520267a847011a530_78.png)

![](img/19fb4dd044ccaf9520267a847011a530_80.png)

通过本课的学习，你已经掌握了DataFrame的创建和基本概念，为后续进行更复杂的数据操作和分析打下了基础。