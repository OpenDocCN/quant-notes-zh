# Python金融量化投资分析：P22：DataFrame的创建 📊

在本节课中，我们将要学习Pandas库中另一个核心数据结构——DataFrame。上一节我们介绍了Series，它是一种一维数据。本节中我们来看看用于处理二维表格数据的DataFrame对象。

DataFrame是一个表格式的数据结构，类似于Excel表格，它包含一组有序的列。DataFrame可以看作是由多个Series组成的字典，并且这些Series共享同一个行索引。

## 通过字典创建DataFrame

DataFrame有多种创建方式。以下是两种常见的基于字典的创建方法。

### 方法一：字典值为列表

我们可以从一个字典创建DataFrame，其中字典的键将成为列名，字典的值（列表）将成为对应列的数据。

```python
import pandas as pd

# 创建一个字典，键为列名，值为列表数据
data = {'one': [1, 2, 3], 'two': [4, 5, 6]}
df = pd.DataFrame(data)
print(df)
```

执行上述代码，将创建一个两列的DataFrame。列`one`的值为`[1, 2, 3]`，列`two`的值为`[4, 5, 6]`。由于未指定行索引，Pandas会自动生成`0, 1, 2`作为行标签。

我们也可以使用`index`参数自定义行索引。

![](img/baf6ce0afeef346c55e1d5caf1792fbd_1.png)

```python
df = pd.DataFrame(data, index=['a', 'b', 'c'])
print(df)
```

![](img/baf6ce0afeef346c55e1d5caf1792fbd_3.png)

### 方法二：字典值为Series

另一种方式是用Series作为字典的值来创建DataFrame。这种方式允许每列数据拥有独立的索引，Pandas会自动根据索引对齐数据。

```python
# 创建两个具有不同索引的Series
s1 = pd.Series([1, 2, 3], index=['a', 'b', 'c'])
s2 = pd.Series([1, 2, 3, 4], index=['b', 'a', 'c', 'd'])

# 用Series字典创建DataFrame
data2 = {'one': s1, 'two': s2}
df2 = pd.DataFrame(data2)
print(df2)
```

在这个例子中，Pandas会自动将`s1`和`s2`的数据按行索引`a, b, c, d`进行对齐。对于`s1`中不存在的索引`d`，其对应值会自动填充为缺失值（NaN）。这个自动对齐功能在处理实际数据时非常方便。

## 从文件读取DataFrame

在实际应用中，我们很少手动构建大型DataFrame，更多的是从外部文件读取数据。Pandas支持多种文件格式。

### 读取CSV文件

最常用的格式是CSV（逗号分隔值）文件。假设我们有一个名为`test.csv`的文件，内容如下：

```
A,B,C
1,2,3
2,4,6
3,6,9
```

我们可以使用`pd.read_csv()`函数读取该文件。

```python
df_from_file = pd.read_csv('test.csv')
print(df_from_file)
```

该函数默认将文件的第一行作为列名（`A, B, C`），并自动生成行索引（`0, 1, 2`）。`read_csv`函数功能强大，包含许多可选参数用于精细控制读取过程，我们将在后续课程中详细介绍。

### 写入CSV文件

同样，我们可以将DataFrame保存到CSV文件，使用`to_csv`方法。

```python
# 将之前创建的df保存到新文件
df.to_csv('test2.csv', index=False)  # index=False表示不将行索引写入文件
```

保存后，缺失值在CSV文件中会显示为空。`to_csv`方法也提供了许多参数来控制输出格式。

除了CSV，Pandas还支持从JSON、Excel、XML等多种格式读取和写入数据，我们将在文件处理的相关章节进行学习。

---

![](img/baf6ce0afeef346c55e1d5caf1792fbd_5.png)

本节课中我们一起学习了DataFrame的创建。我们了解了DataFrame是一个二维表格数据结构，并掌握了两种通过字典创建DataFrame的方法。更重要的是，我们学习了如何从CSV文件读取数据生成DataFrame，以及如何将DataFrame保存回CSV文件。这些是进行金融数据分析最基础且必不可少的操作。