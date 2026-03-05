# 数据分析实战：P9：08 Series的进化之DataFrame 🚀

![](img/285b2d147771a7916de441ca33f120bd_0.png)

![](img/285b2d147771a7916de441ca33f120bd_2.png)

在本节课中，我们将要学习Pandas中一个核心的数据结构——DataFrame。我们将了解它是什么、如何创建它，以及它的一些基本属性。

![](img/285b2d147771a7916de441ca33f120bd_4.png)

## DataFrame是什么？

![](img/285b2d147771a7916de441ca33f120bd_6.png)

![](img/285b2d147771a7916de441ca33f120bd_8.png)

在上一节我们介绍了Series，它是一个一维的数据结构。本节中我们来看看由Series组成的DataFrame。

通过上节课的演示，我们知道一个Series表示一列数据，是一个一维结构。如果有多个Series组合在一起，就会形成多列。多列数据组合在一起，就形成了一个表格型的数据结构。

表格型的数据结构是二维的，因为它有行和列。因此，DataFrame是一个表格型的数据结构。它的设计初衷是将Series的使用场景从一维拓展到二维。

![](img/285b2d147771a7916de441ca33f120bd_10.png)

DataFrame由三个核心部分组成：
*   第一个是行索引。
*   第二个是列索引。
*   第三个是行列索引所对应的值。

![](img/285b2d147771a7916de441ca33f120bd_12.png)

你可以把DataFrame完全理解为MySQL数据库中的一张表，它同样是由行和列组成的表格。

![](img/285b2d147771a7916de441ca33f120bd_14.png)

## 如何创建DataFrame？

![](img/285b2d147771a7916de441ca33f120bd_16.png)

![](img/285b2d147771a7916de441ca33f120bd_18.png)

知道了DataFrame是什么之后，我们再来看一下DataFrame怎么创建。

![](img/285b2d147771a7916de441ca33f120bd_20.png)

首先，我们需要从pandas库中导入DataFrame类。

![](img/285b2d147771a7916de441ca33f120bd_22.png)

![](img/285b2d147771a7916de441ca33f120bd_24.png)

```python
from pandas import DataFrame
```

![](img/285b2d147771a7916de441ca33f120bd_26.png)

接下来，我们调用DataFrame的构造方法进行创建。构造方法的主要参数有：
*   `data`：数据源，可以是二维列表、NumPy数组或字典等。
*   `index`：行索引。
*   `columns`：列索引。
*   `dtype`：元素的数据类型。

![](img/285b2d147771a7916de441ca33f120bd_28.png)

以下是几种常见的创建方式：

**1. 使用二维列表创建**

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```
这将创建一个两行三列的DataFrame。

![](img/285b2d147771a7916de441ca33f120bd_30.png)

![](img/285b2d147771a7916de441ca33f120bd_32.png)

![](img/285b2d147771a7916de441ca33f120bd_34.png)

**2. 使用NumPy数组创建**

![](img/285b2d147771a7916de441ca33f120bd_36.png)

```python
import numpy as np
data_array = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data_array)
print(df2)
```
这将创建一个六行四列的DataFrame，数据是随机整数。

![](img/285b2d147771a7916de441ca33f120bd_38.png)

![](img/285b2d147771a7916de441ca33f120bd_40.png)

![](img/285b2d147771a7916de441ca33f120bd_42.png)

**3. 使用字典创建（最常用、最灵活）**

![](img/285b2d147771a7916de441ca33f120bd_44.png)

![](img/285b2d147771a7916de441ca33f120bd_46.png)

```python
data_dict = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=data_dict)
print(df3)
```
使用字典创建时，**字典的键（key）会自动成为DataFrame的列索引**。此时行索引是默认的隐式索引（0, 1, 2...）。

![](img/285b2d147771a7916de441ca33f120bd_48.png)

![](img/285b2d147771a7916de441ca33f120bd_50.png)

我们也可以指定显示的行索引：
```python
df3 = DataFrame(data=data_dict, index=[‘a‘, ‘b‘, ‘c‘])
print(df3)
```
现在，这个DataFrame的行列索引都变成了显示的。

![](img/285b2d147771a7916de441ca33f120bd_52.png)

## DataFrame的常用属性

下面看一下DataFrame常用的属性，这些属性可以帮助我们快速了解数据的结构。

```python
# 假设 df 是我们上面用字典创建的 DataFrame (df3)
print(df.values)    # 返回底层数据的二维NumPy数组
print(df.columns)   # 返回列索引
print(df.index)     # 返回行索引
print(df.shape)     # 返回DataFrame的形状（行数，列数）
```
需要注意的是，**不能直接使用 `df.dtype` 查看整个DataFrame的数据类型**。因为DataFrame的每一列可以存储不同类型的数据（例如一列是字符串，一列是整数）。我们只能单独查看某一列或某一行的数据类型，因为取出的单列或单行是一个Series，而Series的元素类型是一致的。

![](img/285b2d147771a7916de441ca33f120bd_54.png)

![](img/285b2d147771a7916de441ca33f120bd_56.png)

## 实战练习

![](img/285b2d147771a7916de441ca33f120bd_58.png)

![](img/285b2d147771a7916de441ca33f120bd_60.png)

我们来看一个练习，需要创建如下形式的DataFrame：

![](img/285b2d147771a7916de441ca33f120bd_62.png)

![](img/285b2d147771a7916de441ca33f120bd_64.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

![](img/285b2d147771a7916de441ca33f120bd_66.png)

以下是使用字典创建这个DataFrame的方法，这是最便捷的方式：

![](img/285b2d147771a7916de441ca33f120bd_68.png)

![](img/285b2d147771a7916de441ca33f120bd_70.png)

```python
# 准备数据字典，键将成为列名
data = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}
# 创建DataFrame，并指定行索引
df_exercise = DataFrame(data=data, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

## 总结

![](img/285b2d147771a7916de441ca33f120bd_72.png)

![](img/285b2d147771a7916de441ca33f120bd_74.png)

本节课中我们一起学习了Pandas的核心数据结构DataFrame。
*   我们首先明确了**DataFrame是一个二维的表格型数据结构**，由行索引、列索引和值三部分组成。
*   接着，我们掌握了三种创建DataFrame的方法：**使用二维列表、NumPy数组和字典**，其中使用字典创建最为灵活便捷。
*   然后，我们了解了DataFrame的几个**常用属性**，如 `.values`、`.columns`、`.index` 和 `.shape`。
*   最后，我们通过一个练习巩固了如何使用字典来创建具有指定行列索引的DataFrame。

![](img/285b2d147771a7916de441ca33f120bd_76.png)

![](img/285b2d147771a7916de441ca33f120bd_78.png)

理解并熟练创建DataFrame，是后续进行数据操作和分析的基础。