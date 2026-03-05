# Python金融量化分析：P24：DataFrame的创建 📊

在本节课中，我们将要学习Pandas中另一个核心数据结构——DataFrame。上一节我们介绍了Series，它是一个一维的数据对象。本节中我们来看看DataFrame，它是一个二维的、表格型的数据结构，类似于Excel表格，包含多列数据。

DataFrame可以看作是由多个Series组成的字典，并且这些Series共享同一个行索引。接下来，我们将学习几种创建DataFrame的方法。

## 从字典创建DataFrame

DataFrame可以通过多种方式创建。以下是第一种常见方法：从一个字典创建。字典的键将成为列名，字典的值（列表）将成为对应列的数据。

```python
import pandas as pd

# 创建一个字典，键为列名，值为列表数据
data = {'one': [1, 2, 3], 'two': [4, 5, 6]}
df = pd.DataFrame(data)
print(df)
```

执行上述代码，会创建一个两列的DataFrame。列“one”的值为[1, 2, 3]，列“two”的值为[4, 5, 6]。由于没有指定行索引，Pandas会自动生成整数索引0, 1, 2。

如果我们想自定义行索引，可以使用`index`参数。

![](img/79c803a7a3ea82da555e94d7485bc206_1.png)

```python
df_custom_index = pd.DataFrame(data, index=['a', 'b', 'c'])
print(df_custom_index)
```

![](img/79c803a7a3ea82da555e94d7485bc206_3.png)

## 从Series字典创建DataFrame

第二种创建方式是从一个由Series组成的字典创建。这种方式的一个强大之处在于，Pandas会自动根据索引对齐数据。

```python
# 创建两个具有不同索引的Series
s1 = pd.Series([1, 2, 3], index=['a', 'b', 'c'])
s2 = pd.Series([1, 2, 3, 4], index=['b', 'a', 'c', 'd'])

# 用Series字典创建DataFrame
df_from_series = pd.DataFrame({'one': s1, 'two': s2})
print(df_from_series)
```

在这个例子中，列“one”的索引是[‘a’, ‘b’, ‘c’]，列“two”的索引是[‘b’, ‘a’, ‘c’, ‘d’]。当它们组合成DataFrame时，Pandas会自动将所有索引（‘a’, ‘b’, ‘c’, ‘d’）对齐。对于某个Series中不存在的索引位置（如‘d’在s1中不存在），Pandas会将其填充为缺失值（NaN）。这个自动对齐功能在处理实际数据时非常方便。

## 从文件读取创建DataFrame

在实际应用中，我们很少手动输入数据来创建DataFrame，更多的是从外部文件读取。Pandas支持多种文件格式，最常用的是CSV文件。

CSV文件是一种纯文本格式，用逗号分隔值，每一行是一条记录。以下是一个示例CSV文件（test.csv）的内容：
```
A,B,C
1,2,3
2,4,6
3,6,9
```

Pandas提供了`read_csv()`函数来读取CSV文件并创建DataFrame。

```python
# 从CSV文件读取数据
df_from_file = pd.read_csv('test.csv')
print(df_from_file)
```

执行后，`read_csv()`函数会将文件的第一行（A, B, C）识别为列名，并自动生成行索引0, 1, 2。`read_csv()`函数功能非常强大，有众多参数可以处理不同的数据格式，我们将在后续课程中详细介绍。

## 将DataFrame保存到文件

创建或处理完DataFrame后，我们也可以将其保存回文件。使用`to_csv()`方法可以将DataFrame写入CSV文件。

```python
# 将DataFrame保存为CSV文件
df.to_csv('test2.csv', index=False)  # index=False表示不将行索引写入文件
```

保存后，可以用文本编辑器打开`test2.csv`查看内容。同样，`to_csv()`方法也有许多参数可以控制输出格式。

除了CSV，Pandas还支持从JSON、Excel、XML等多种格式读写数据，我们会在后续的文件处理章节中学习。

![](img/79c803a7a3ea82da555e94d7485bc206_5.png)

本节课中我们一起学习了DataFrame的创建。我们介绍了三种主要方式：从列表字典创建、从Series字典创建以及从文件（特别是CSV文件）读取创建。DataFrame是Pandas进行数据分析的基石，理解其创建方式是后续所有操作的第一步。