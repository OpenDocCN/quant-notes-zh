# Python金融量化投资分析与股票交易：P21：DataFrame的创建 📊

在本节课中，我们将要学习Pandas中另一个核心数据结构——DataFrame。上一节我们介绍了Series，它是一个一维的数据对象。本节中我们来看看用于处理二维表格数据的DataFrame。

DataFrame是一个表格式的数据结构，类似于Excel表格，它含有一组有序的列。DataFrame可以看作是由多个Series组成的字典，并且这些Series共享同一个行索引。

## 从字典创建DataFrame

DataFrame有多种创建方式。以下是两种常见的方法。

### 方法一：字典的值为列表

我们可以从一个字典创建DataFrame，其中字典的键将成为列名，字典的值（列表）将成为对应列的数据。

```python
import pandas as pd

data = {
    ‘one‘: [1, 2, 3],
    ‘two‘: [4, 5, 6]
}
df = pd.DataFrame(data)
print(df)
```

执行上述代码，会创建一个两列的DataFrame。如果没有指定行索引，Pandas会自动生成从0开始的整数索引。

![](img/6c0a3256528956b22ad9acfe4c9c1f7e_1.png)

如果想指定行索引，可以使用`index`参数。

![](img/6c0a3256528956b22ad9acfe4c9c1f7e_3.png)

```python
df = pd.DataFrame(data, index=[‘a‘, ‘b‘, ‘c‘])
print(df)
```

### 方法二：字典的值为Series

另一种创建方式是从一个字典创建，但字典的值是Series对象。每个Series可以有自己的索引。

```python
data2 = {
    ‘one‘: pd.Series([1, 2, 3], index=[‘a‘, ‘b‘, ‘c‘]),
    ‘two‘: pd.Series([1, 2, 3, 4], index=[‘b‘, ‘a‘, ‘c‘, ‘d‘])
}
df2 = pd.DataFrame(data2)
print(df2)
```

当使用这种方式时，Pandas会自动根据所有Series的索引进行对齐。对于某个索引位置，如果某个Series没有值，则会用缺失值（NaN）填充。这个特性非常方便，省去了手动对齐数据的麻烦。

## 从文件读取DataFrame

在实际应用中，我们很少手动构建数据，更多的是从外部文件读取。Pandas支持多种文件格式。

### 读取CSV文件

最常用的格式之一是CSV（逗号分隔值）文件。Pandas提供了`read_csv()`函数来读取。

假设有一个名为`test.csv`的文件，内容如下：
```
A,B,C
1,2,3
2,4,6
3,6,9
```

我们可以这样读取它：

```python
df_from_file = pd.read_csv(‘test.csv‘)
print(df_from_file)
```

`read_csv()`函数默认将文件的第一行作为列名（表头），并自动生成行索引。这个函数功能强大，有众多可选参数用于处理不同的数据格式，我们将在后续课程中详细介绍。

### 写入CSV文件

将DataFrame保存到CSV文件同样简单，使用`to_csv()`方法即可。

```python
df.to_csv(‘test2.csv‘)
```

执行后，DataFrame的内容就会被写入到`test2.csv`文件中。对于缺失值，默认会保存为空字符串，这个行为也可以通过参数调整。

除了CSV格式，Pandas还支持从JSON、Excel、XML等多种格式读取和写入数据，我们将在后面的文件处理部分进行学习。

![](img/6c0a3256528956b22ad9acfe4c9c1f7e_5.png)

本节课中我们一起学习了DataFrame的创建。我们了解了DataFrame是一个二维表格数据结构，并掌握了两种从字典创建DataFrame的方法。更重要的是，我们学习了如何从CSV文件读取数据以及如何将数据保存回CSV文件，这是进行数据分析的基础步骤。