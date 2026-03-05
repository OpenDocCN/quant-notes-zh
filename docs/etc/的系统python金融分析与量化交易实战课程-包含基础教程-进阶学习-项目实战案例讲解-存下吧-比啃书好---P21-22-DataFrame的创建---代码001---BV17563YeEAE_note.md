# 量化交易教程：P21：DataFrame的创建

在本节课中，我们将要学习Pandas库中的第二个核心数据结构——DataFrame。DataFrame是一个二维的、表格型的数据结构，它包含一组有序的列，每列可以是不同的值类型（数值、字符串、布尔值等），并且拥有行索引和列索引。它类似于Excel表格或SQL数据库中的表，是进行金融数据分析和量化交易最常用的工具。

上一节我们介绍了Pandas的一维数据结构Series。本节中我们来看看功能更强大的二维数据结构DataFrame。

## DataFrame的基本概念

DataFrame是一个二维表格数据结构，包含行和列。它可以看作是由多个Series组成的字典，这些Series共享同一个行索引。DataFrame是进行数据清洗、分析和建模的核心对象。

## 创建DataFrame的方法

有多种方法可以创建DataFrame对象。以下是几种常见的创建方式。

### 1. 从字典创建（值为列表）

这是最直观的创建方式之一。字典的键将成为DataFrame的列名，字典的值（列表）将成为对应列的数据。

```python
import pandas as pd

![](img/ec3d46c2b34eac1f65df2f7943285852_1.png)

# 创建一个字典，键为列名，值为该列的数据列表
data = {
    ‘one‘: [1, 2, 3],
    ‘two‘: [4, 5, 6]
}

![](img/ec3d46c2b34eac1f65df2f7943285852_3.png)

# 使用pd.DataFrame()函数创建DataFrame
df = pd.DataFrame(data)
print(df)
```
执行以上代码，将创建一个两列（‘one‘, ‘two‘）三行的DataFrame。由于没有指定行索引（index），Pandas会自动生成整数索引0, 1, 2。

如果想指定行索引，可以使用`index`参数。

```python
df_with_index = pd.DataFrame(data, index=[‘a‘, ‘b‘, ‘c‘])
print(df_with_index)
```
现在，DataFrame的行索引变成了‘a‘, ‘b‘, ‘c‘。

### 2. 从字典创建（值为Series）

当字典的值是Series时，Pandas会自动根据索引对齐数据。这在处理具有不同索引的数据时非常有用。

```python
# 创建两个具有不同索引的Series
s1 = pd.Series([1, 2, 3], index=[‘a‘, ‘b‘, ‘c‘])
s2 = pd.Series([1, 2, 3, 4], index=[‘b‘, ‘a‘, ‘c‘, ‘d‘])

# 用这两个Series作为值创建字典
data_from_series = {
    ‘col1‘: s1,
    ‘col2‘: s2
}

# 创建DataFrame
df_series = pd.DataFrame(data_from_series)
print(df_series)
```
在这个例子中，`s1`没有索引‘d‘，`s2`没有索引‘a‘, ‘b‘, ‘c‘之外的值。创建DataFrame时，Pandas会自动将所有索引（‘a‘, ‘b‘, ‘c‘, ‘d‘）进行合并对齐。对于某个Series中不存在的索引位置，Pandas会填充缺失值（NaN）。这个功能使得数据合并变得非常方便。

### 3. 从文件读取创建

在实际工作中，我们很少手动输入数据来创建DataFrame，更多的是从外部文件（如CSV、Excel）中读取数据。Pandas提供了强大的文件读取功能。

最常用的格式是CSV（逗号分隔值）文件。以下是一个名为`test.csv`的文件内容示例：
```
A,B,C
1,2,3
2,4,6
3,6,9
```
使用`pd.read_csv()`函数可以轻松读取该文件并创建DataFrame。

```python
# 从CSV文件读取数据
df_from_file = pd.read_csv(‘test.csv‘)
print(df_from_file)
```
`read_csv`函数默认将文件的第一行作为列名（header），并自动生成整数行索引。该函数有众多参数，可以应对不同的文件格式需求，我们将在后续课程中详细介绍。

同样，我们可以将DataFrame保存回CSV文件。

```python
# 将DataFrame保存为CSV文件
df.to_csv(‘test2.csv‘, index=False) # index=False表示不将行索引写入文件
```
`to_csv`方法允许我们控制是否写入索引、如何处理缺失值等。

除了CSV，Pandas还支持从JSON、Excel、HTML、SQL数据库等多种数据源读取和写入数据。

## 总结

本节课中我们一起学习了Pandas中DataFrame的创建方法。我们了解到DataFrame是一个二维表格数据结构，是数据分析的核心。我们掌握了三种创建DataFrame的常用方法：
1.  **从字典创建（列表作为值）**：简单直观，适合手动构建小型数据集。
2.  **从字典创建（Series作为值）**：Pandas会自动根据索引对齐数据，处理缺失值，适合合并多个数据序列。
3.  **从文件读取创建**：这是最常用的方式，`pd.read_csv()`和`df.to_csv()`是读写CSV文件的标准方法。

![](img/ec3d46c2b34eac1f65df2f7943285852_5.png)

理解如何创建和获取DataFrame是进行后续所有数据操作的第一步。在下一节中，我们将学习如何查看和选取DataFrame中的数据。