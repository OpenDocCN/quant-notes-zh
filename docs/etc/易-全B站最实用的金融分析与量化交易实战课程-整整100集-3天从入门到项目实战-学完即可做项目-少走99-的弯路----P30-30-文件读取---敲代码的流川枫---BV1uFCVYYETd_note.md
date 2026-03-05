# 量化交易实战课程：P30：文件读取 📂

在本节课中，我们将学习如何使用Pandas库读取外部文件数据。这是数据分析与量化交易实战中获取真实数据的关键步骤。

---

![](img/2b1601b74af856fb1a01bbc0e94182c0_1.png)

![](img/2b1601b74af856fb1a01bbc0e94182c0_2.png)

上一节我们介绍了Pandas在数据处理、对齐、缺失值处理和时间序列等方面的功能。本节中，我们来看看如何从文件中读取数据。

在日常编程中，我们通常不会手动输入数据来创建DataFrame。真实数据一般存储在文件中。因此，学习如何从文件读取数据以及向文件写入数据至关重要。

最常用的数据文件格式是CSV。CSV文件本质上是一个文本文件，其中每一行代表一条记录，每个单元格（数据项）由逗号分隔。两个逗号之间的部分就是一个单元格内存储的数据。

`read_csv`函数可以将CSV文件的内容读取到Pandas的DataFrame中。

以下是一个示例文件，它包含了某只股票从2007年3月1日到2017年11月10日左右的行情数据。其列包括：序号、时间、开盘价、收盘价、最高价、最低价、成交量和股票代码。

我们使用`read_csv`函数读取这个文件。

```python
import pandas as pd
df = pd.read_csv('stock_data.csv')
print(df.head())
```

读取后，大部分数据被成功加载。但存在几个问题：首先，文件原有的“序号”列（0,1,2...）被读取为一列数据，同时Pandas又自动生成了一个新的整数索引（0,1,2...）。其次，第一行（列名行）上方有一个空单元格，被Pandas命名为“Unnamed: 0”。

如果想指定某一列作为行索引，可以使用`index_col`参数。

```python
# 将第0列作为索引
df = pd.read_csv('stock_data.csv', index_col=0)
# 或将名为‘date’的列作为索引
df = pd.read_csv('stock_data.csv', index_col='date')
```

将“date”列设为索引后，我们发现它虽然显示为日期，但其数据类型是字符串，而非时间对象。要将其转换为时间序列，需要使用`parse_dates`参数。

```python
# 方法一：尝试解析所有可识别为日期的列
df = pd.read_csv('stock_data.csv', index_col='date', parse_dates=True)
# 方法二：明确指定要解析的列
df = pd.read_csv('stock_data.csv', index_col='date', parse_dates=['date'])
print(type(df.index))  # 输出应为 pandas.core.indexes.datetimes.DatetimeIndex
```

如果数据文件没有列名（即第一行就是数据），直接读取会导致第一行数据被误认为是列名。这时需要使用`header`和`names`参数。

```python
# 文件无列名，让Pandas自动生成列名（0,1,2...）
df = pd.read_csv('data_without_header.csv', header=None)
# 文件无列名，手动指定列名
df = pd.read_csv('data_without_header.csv', header=None, names=['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'])
```

除了`read_csv`，Pandas还提供了`read_table`函数。两者的主要区别在于默认分隔符：`read_csv`默认是逗号，而`read_table`默认是制表符（`\t`）。两者都可以通过`sep`参数指定分隔符。

以下是`read_csv`和`read_table`的一些常用参数：

*   **`sep`**: 指定分隔符，默认为逗号（`,`）。可以传入字符串（如空格`‘ ‘`）或正则表达式（如`r‘\s+‘`匹配任意空白字符）。
*   **`header`**: 指定哪一行作为列名。默认为0（第一行）。设置为`None`表示文件没有列名。
*   **`names`**: 当`header=None`时，用于手动指定列名列表。
*   **`index_col`**: 指定用作行索引的列编号或列名。
*   **`skiprows`**: 指定要跳过的行号列表（从文件开始算起）。
*   **`na_values`**: 指定哪些字符串应被识别为缺失值（NaN）。这对于处理非标准缺失值标记（如“N/A”、“NULL”、“-”）非常有用。
*   **`parse_dates`**: 尝试将指定列解析为日期时间格式。可以是布尔值（尝试解析所有列）或列表（指定列名/编号）。

关于`na_values`参数，这里有一个重要示例：如果数据中缺失值被标记为“N”（而非标准的“NaN”或空值），Pandas会将其视为字符串，导致整列数据类型变为`object`。使用`na_values`可以解决此问题。

```python
# 假设文件中用‘N‘表示缺失值
df = pd.read_csv('data_with_N.csv')
print(df['某列'].dtype)  # 可能输出 object
print(df.loc[0, ‘某列‘])  # 输出 ‘N‘ (字符串)

# 使用na_values参数
df_corrected = pd.read_csv('data_with_N.csv', na_values=['N'])
print(df_corrected['某列'].dtype)  # 输出 float64
print(pd.isna(df_corrected.loc[0, ‘某列‘]))  # 输出 True
```

---

![](img/2b1601b74af856fb1a01bbc0e94182c0_4.png)

本节课中我们一起学习了使用Pandas读取文件的核心方法。我们重点掌握了`read_csv`函数，了解了如何处理索引列、解析日期时间、处理无列名文件以及识别自定义缺失值标记。这些技能是后续进行金融数据分析和量化交易项目的基础。下一节，我们将学习如何将处理好的数据写入文件。