# 量化交易：P22：DataFrame的创建 📊

在本节课中，我们将要学习Pandas库中的第二个核心数据结构——DataFrame。DataFrame是一个二维的、表格型的数据结构，它包含一组有序的列，每列可以是不同的值类型（数值、字符串、布尔值等），非常类似于Excel表格或SQL数据库中的表。我们将学习如何创建DataFrame，并了解其基本特性。

上一节我们介绍了Pandas的一维数据结构Series，它只有一列。但在实际数据分析中，我们经常需要处理包含多行多列的二维表格数据。本节中我们来看看Pandas中用于处理这类数据的主要对象：DataFrame。

## 什么是DataFrame？ 🤔

DataFrame是一个表格式的数据结构，含有一组列。它可以看作是由多个Series组成的字典，并且这些Series共享同一个行索引。这意味着DataFrame既有行索引（index），也有列索引（columns）。

## 如何创建DataFrame？ 🛠️

DataFrame有多种创建方式。以下是几种常见的方法。

### 1. 从字典创建（值为列表）

这是最直观的创建方式之一。字典的键（key）将成为DataFrame的列名，字典的值（value）——一个列表——将成为该列的数据。

```python
import pandas as pd

# 创建一个字典，键是列名，值是列表形式的数据
data = {
    ‘one‘: [1, 2, 3],
    ‘two‘: [4, 5, 6]
}

![](img/d5cf9f19ce64d339d7179de46e48fe83_1.png)

# 使用pd.DataFrame()函数创建DataFrame
df = pd.DataFrame(data)
print(df)
```
执行上述代码，将创建一个两列（‘one‘和‘two‘）、三行的DataFrame。由于没有指定行索引，Pandas会自动生成从0开始的整数索引（0, 1, 2）。

![](img/d5cf9f19ce64d339d7179de46e48fe83_3.png)

我们也可以使用`index`参数来指定自定义的行索引标签。

```python
df_custom_index = pd.DataFrame(data, index=[‘a‘, ‘b‘, ‘c‘])
print(df_custom_index)
```
现在，DataFrame的行索引将变为[‘a‘, ‘b‘, ‘c‘]。

### 2. 从字典创建（值为Series）

当字典的值是Series时，创建DataFrame的过程会涉及数据对齐。Pandas会自动根据Series的索引进行匹配，如果某个索引在另一个Series中不存在，则对应位置会填充为缺失值（NaN）。

```python
# 创建两个具有不同索引的Series
s1 = pd.Series([1, 2, 3], index=[‘a‘, ‘b‘, ‘c‘])
s2 = pd.Series([1, 2, 3, 4], index=[‘b‘, ‘a‘, ‘c‘, ‘d‘])

# 用这两个Series作为值创建字典
data_series = {
    ‘col1‘: s1,
    ‘col2‘: s2
}

df_from_series = pd.DataFrame(data_series)
print(df_from_series)
```
在这个例子中，`col1`列在索引‘d‘处没有值，因此显示为NaN。`col2`列在索引‘a‘, ‘b‘, ‘c‘, ‘d‘处都有值。Pandas自动将所有索引（‘a‘, ‘b‘, ‘c‘, ‘d‘）对齐，形成了最终的行索引。这个特性非常有用，省去了手动对齐数据的麻烦。

### 3. 从文件读取（以CSV为例） 📁

在实际项目中，我们很少手动输入数据来创建DataFrame，更多的是从外部文件（如CSV、Excel、JSON）中读取数据。Pandas提供了非常便捷的函数来完成这个操作。

以下是从CSV文件读取数据的基本方法。CSV文件是一种用逗号分隔值的纯文本文件。

假设我们有一个名为`test.csv`的文件，内容如下：
```
A,B,C
1,2,3
2,4,6
3,6,9
```

我们可以使用`pd.read_csv()`函数来读取它：

```python
# 从CSV文件读取数据创建DataFrame
df_from_csv = pd.read_csv(‘test.csv‘)
print(df_from_csv)
```
`read_csv`函数默认将文件的第一行作为列名（即`A`, `B`, `C`），并自动生成从0开始的整数行索引。我们将在后续课程中详细介绍这个函数的各种参数，以应对不同的数据格式需求。

### 4. 将DataFrame保存到文件 💾

同样，我们可以轻松地将DataFrame保存回文件，例如保存为CSV格式。

```python
# 将之前创建的df保存为新的CSV文件
df.to_csv(‘test2.csv‘, index=False) # index=False表示不将行索引写入文件
```
`to_csv`方法将DataFrame写入指定的CSV文件。参数`index=False`可以避免将行索引也写入文件，让文件更简洁。关于缺失值（NaN）在文件中的表示方式（默认为空），也可以通过参数进行设置。

除了CSV，Pandas也支持将DataFrame写入Excel、JSON、HTML等多种格式，这些我们会在后续的文件处理章节中学习。

## 总结 📝

本节课中我们一起学习了Pandas中至关重要的DataFrame对象。我们了解到：
*   **DataFrame**是一个二维表格数据结构，由行索引和列索引组成。
*   掌握了**四种创建DataFrame**的基本方法：
    1.  从**值为列表的字典**创建。
    2.  从**值为Series的字典**创建（具有自动索引对齐的特性）。
    3.  使用`pd.read_csv()`从**CSV文件读取**创建（最常用的数据导入方式）。
    4.  使用`df.to_csv()`将DataFrame**保存到CSV文件**。
*   理解了DataFrame是进行金融数据分析和量化交易的基础，后续几乎所有数据操作都将围绕它展开。

![](img/d5cf9f19ce64d339d7179de46e48fe83_5.png)

在下一节，我们将深入探讨如何访问和操作DataFrame中的数据，例如如何选取特定的行、列或单元格。