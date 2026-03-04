# Python金融量化：P8：08：DataFrame基础入门 📊

![](img/e948087daaa41f9ed0d09fe8b2f6449e_0.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_2.png)

在本节课中，我们将要学习Pandas中另一个核心数据结构——**DataFrame**。我们将了解它是什么、如何创建它，并掌握其基本属性。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_4.png)

## 什么是DataFrame？ 🤔

![](img/e948087daaa41f9ed0d09fe8b2f6449e_6.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_8.png)

上一节我们介绍了Series，它是一个一维的数据结构。本节中我们来看看DataFrame。

DataFrame是由多个Series组成的。既然Series是一维的，那么由它们组成的DataFrame就是一个**二维的表格型数据结构**。它拥有行和列，就像数据库中的一张表或Excel中的一个工作表。

DataFrame由三个核心部分组成：
1.  **行索引** (Index)
2.  **列索引** (Columns)
3.  **数据值** (Values)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_10.png)

## 如何创建DataFrame？ 🛠️

要创建DataFrame，首先需要从Pandas库中导入它。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_12.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_14.png)

```python
from pandas import DataFrame
```

![](img/e948087daaa41f9ed0d09fe8b2f6449e_16.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_18.png)

以下是几种常见的创建DataFrame的方法。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_20.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_22.png)

### 方法一：使用二维列表

![](img/e948087daaa41f9ed0d09fe8b2f6449e_24.png)

我们可以直接传递一个二维列表作为数据源。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_26.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_28.png)

```python
df1 = DataFrame(data=[[1, 2, 3], [4, 5, 6]])
print(df1)
```
输出是一个2行3列的表格。

### 方法二：使用NumPy数组

![](img/e948087daaa41f9ed0d09fe8b2f6449e_30.png)

我们也可以使用NumPy生成的数组作为数据。

```python
import numpy as np
data = np.random.randint(0, 100, size=(6, 4))
df2 = DataFrame(data=data)
print(df2)
```
这将创建一个6行4列的DataFrame，数据是0到100之间的随机整数。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_32.png)

### 方法三：使用字典（最常用且灵活）

![](img/e948087daaa41f9ed0d09fe8b2f6449e_34.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_36.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_38.png)

使用字典创建DataFrame非常方便，**字典的键（key）会自动成为DataFrame的列索引**。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_40.png)

```python
data_dict = {
    ‘name‘: [‘张三‘, ‘李四‘, ‘王五‘],
    ‘salary‘: [1000, 2000, 3000]
}
df3 = DataFrame(data=data_dict)
print(df3)
```
此时，行索引是默认的0, 1, 2（隐式索引），列索引是‘name‘和‘salary‘（显示索引）。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_42.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_44.png)

我们还可以在创建时指定行索引。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_46.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_48.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_50.png)

```python
df3 = DataFrame(data=data_dict, index=[‘A‘, ‘B‘, ‘C‘])
print(df3)
```
现在，行索引也变成了显示索引‘A‘, ‘B‘, ‘C‘。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_52.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_54.png)

## DataFrame的常用属性 📋

创建好DataFrame后，我们可以通过一些属性来查看其信息。

以下是几个关键属性：
*   **`df.values`**: 返回DataFrame中的数据，是一个二维的NumPy数组。
*   **`df.columns`**: 返回列索引。
*   **`df.index`**: 返回行索引。
*   **`df.shape`**: 返回DataFrame的形状（行数，列数）。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_56.png)

**注意**：与Series不同，DataFrame没有直接的`.dtype`属性。因为DataFrame的不同列可以存储不同类型的数据（例如一列是字符串，另一列是整数）。要查看数据类型，需要单独查看某一列（返回一个Series）的数据类型。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_58.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_60.png)

## 练习：创建指定表格 🧩

![](img/e948087daaa41f9ed0d09fe8b2f6449e_62.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_64.png)

现在，让我们通过一个练习来巩固所学知识。我们需要创建如下所示的DataFrame：

![](img/e948087daaa41f9ed0d09fe8b2f6449e_66.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_68.png)

|       | 张三 | 李四 |
| :---- | :--- | :--- |
| 语文  | 150  | 0    |
| 数学  | 150  | 0    |
| 英语  | 150  | 0    |
| 理综  | 150  | 0    |

使用字典创建是最便捷的方法：

![](img/e948087daaa41f9ed0d09fe8b2f6449e_70.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_72.png)

```python
exercise_dict = {
    ‘张三‘: [150, 150, 150, 150],
    ‘李四‘: [0, 0, 0, 0]
}
df_exercise = DataFrame(data=exercise_dict, index=[‘语文‘, ‘数学‘, ‘英语‘, ‘理综‘])
print(df_exercise)
```

![](img/e948087daaa41f9ed0d09fe8b2f6449e_74.png)

## 总结 📝

![](img/e948087daaa41f9ed0d09fe8b2f6449e_76.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_78.png)

本节课中我们一起学习了：
1.  **DataFrame的本质**：一个二维的表格型数据结构，由行索引、列索引和数据值组成。
2.  **DataFrame的创建**：掌握了三种主要创建方式——使用**二维列表**、**NumPy数组**和**字典**，其中字典方式最为灵活。
3.  **DataFrame的属性**：了解了如何通过`.values`、`.columns`、`.index`、`.shape`等属性查看DataFrame的基本信息。

![](img/e948087daaa41f9ed0d09fe8b2f6449e_80.png)

![](img/e948087daaa41f9ed0d09fe8b2f6449e_82.png)

通过本节学习，你已经掌握了DataFrame的基础知识，它是进行金融数据分析和量化交易中处理表格数据的核心工具。下一节，我们将学习如何对DataFrame进行更深入的操作和查询。