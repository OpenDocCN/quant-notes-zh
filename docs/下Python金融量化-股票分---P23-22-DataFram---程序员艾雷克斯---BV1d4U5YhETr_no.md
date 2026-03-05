# Python金融量化投资分析与股票交易：P23：DataFrame的创建 📊

在本节课中，我们将要学习Pandas库中另一个核心数据结构——**DataFrame**。DataFrame是一个二维的、表格型的数据结构，类似于Excel表格或SQL数据表，它包含一组有序的列，每列可以是不同的值类型（数值、字符串、布尔值等）。我们将学习如何创建DataFrame，并了解其基本特性。

上一节我们介绍了Pandas的一维数据结构Series。本节中我们来看看二维的表格数据是如何在Pandas中表示的。

## 什么是DataFrame？ 🤔

DataFrame是一个二维的、表格型的数据结构。它包含行和列，可以看作是由多个Series组成的字典，并且这些Series共享同一个行索引。

## 创建DataFrame的方法 🛠️

DataFrame有多种创建方式。以下是两种最常用的方法。

### 方法一：从字典创建（值为列表）

我们可以通过一个字典来创建DataFrame，其中字典的键将成为列名，字典的值（列表）将成为对应列的数据。

```python
import pandas as pd

# 创建一个字典，键是列名，值是列数据（列表）
data = {
    ‘one‘: [1, 2, 3],
    ‘two‘: [4, 5, 6]
}

![](img/93f14352cf0836361cf81de3ad27b38c_1.png)

![](img/93f14352cf0836361cf81de3ad27b38c_3.png)

# 使用pd.DataFrame()函数创建DataFrame
df = pd.DataFrame(data)
print(df)
```

执行上述代码，将创建一个两列的DataFrame。如果没有指定行索引（`index`），Pandas会自动生成从0开始的整数索引。

如果想指定行索引的标签，可以使用`index`参数。

```python
df_with_index = pd.DataFrame(data, index=[‘a‘, ‘b‘, ‘c‘])
print(df_with_index)
```

### 方法二：从字典创建（值为Series）

我们也可以使用字典来创建DataFrame，但字典的值是Series对象。这种方式允许每列数据拥有自己的索引，Pandas在创建DataFrame时会自动根据索引对齐数据。

```python
# 创建两个Series，拥有不同的索引
s1 = pd.Series([1, 2, 3], index=[‘a‘, ‘b‘, ‘c‘])
s2 = pd.Series([1, 2, 3, 4], index=[‘b‘, ‘a‘, ‘c‘, ‘d‘])

# 用Series字典创建DataFrame
data_series = {
    ‘one‘: s1,
    ‘two‘: s2
}
df_from_series = pd.DataFrame(data_series)
print(df_from_series)
```

在这个例子中，两个Series的索引不完全一致。Pandas会自动根据所有出现的索引（‘a‘, ‘b‘, ‘c‘, ‘d‘）进行对齐。对于某个Series中不存在的索引位置，Pandas会填入缺失值（NaN）。这个自动对齐功能在处理金融时间序列等数据时非常有用。

## 从文件读取DataFrame 📁

在实际应用中，我们很少手动输入数据来创建DataFrame，更多的是从外部文件（如CSV、Excel）中读取数据。Pandas提供了强大的文件读取功能。

以下是使用`read_csv`函数从CSV文件创建DataFrame的简单示例。

假设我们有一个名为`test.csv`的文件，内容如下：
```
A,B,C
1,2,3
2,4,6
3,6,9
```

```python
# 从CSV文件读取数据创建DataFrame
df_from_file = pd.read_csv(‘test.csv‘)
print(df_from_file)
```
`read_csv`函数默认将文件的第一行作为列名（`header=0`），并自动生成行索引。

## 将DataFrame保存到文件 💾

同样，我们可以将DataFrame对象保存到文件中，最常用的格式是CSV。

```python
# 将之前创建的DataFrame保存为CSV文件
df.to_csv(‘test2.csv‘, index=False) # index=False表示不将行索引写入文件
```

`to_csv`方法会将DataFrame的内容写入指定文件。参数`index=False`可以避免将行索引作为一列写入文件。

除了CSV格式，Pandas还支持从JSON、Excel、HTML等多种格式读写数据，这些功能我们将在后续课程中详细介绍。

![](img/93f14352cf0836361cf81de3ad27b38c_5.png)

本节课中我们一起学习了DataFrame的创建。我们了解了DataFrame是一个二维表格数据结构，并掌握了通过字典（列表或Series）手动创建DataFrame的方法。更重要的是，我们学习了如何从CSV文件读取数据来创建DataFrame，以及如何将DataFrame保存回CSV文件。这是进行金融数据分析最基础也是最重要的一步，为后续的数据清洗、分析和可视化打下了坚实的基础。