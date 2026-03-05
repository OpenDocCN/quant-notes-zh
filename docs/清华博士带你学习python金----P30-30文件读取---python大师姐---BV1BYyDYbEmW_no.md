# Python金融量化投资分析与股票交易：P30：文件读取 📂

在本节课中，我们将学习如何使用Pandas库读取外部文件数据。这是数据分析中至关重要的一步，因为真实世界的数据通常存储在文件中，而非手动输入。

![](img/791b7b6c00f1056b5787868e287d226b_1.png)

## 概述
之前我们已经学习了Pandas的多种功能，包括灵活的数据操作、数据对齐、缺失值处理和时间序列等。本节将介绍最后一部分：文件处理。我们将重点学习如何从CSV文件中读取数据，并掌握`read_csv`函数的关键参数。

![](img/791b7b6c00f1056b5787868e287d226b_2.png)

## CSV文件格式
CSV文件是一种纯文本文件，常用于存储表格数据。其特点是每一行代表一条记录，而每行中的各个字段（单元格）由逗号分隔。这类似于Excel表格的结构。

## 使用`read_csv`函数读取数据
`read_csv`函数是Pandas中用于读取CSV文件的核心函数。以下是一个基本示例：

```python
import pandas as pd
df = pd.read_csv(‘filename.csv’)
```

### 读取示例数据
假设我们有一个名为`stock_data.csv`的文件，记录了某支股票从2007年3月1日到2017年11月10日的行情数据。其列包括：序号、时间、开盘价、收盘价、最高价、最低价、成交量和股票代码。

直接读取后，Pandas会自动生成一个从0开始的整数索引。但原始数据中的第一列（序号）也被读取为一列数据，这可能导致索引混乱。

### 指定行索引列
为了使用数据中的某一列作为行索引，可以使用`index_col`参数。

```python
# 使用第0列作为索引
df = pd.read_csv(‘stock_data.csv’, index_col=0)

# 使用‘date’列作为索引
df = pd.read_csv(‘stock_data.csv’, index_col=‘date’)
```

### 解析日期列
默认情况下，日期列会被读取为字符串。为了将其转换为Pandas的`DatetimeIndex`（时间序列对象），需要使用`parse_dates`参数。

```python
# 将所有可解析为日期的列都进行转换
df = pd.read_csv(‘stock_data.csv’, parse_dates=True)

# 仅解析指定的列（例如‘date’列）
df = pd.read_csv(‘stock_data.csv’, parse_dates=[‘date’])
```

转换后，`df.index`的类型将变为`DatetimeIndex`，便于后续的时间序列分析。

### 处理无列名的文件
如果CSV文件的第一行不是列名（即没有表头），Pandas默认会将第一行数据误认为列名。此时，需要使用`header`和`names`参数。

```python
# 文件无表头，Pandas自动生成0,1,2…作为列名
df = pd.read_csv(‘no_header.csv’, header=None)

# 文件无表头，手动指定列名
column_names = [‘A‘, ’B‘, ’C‘, ’D‘, ’E‘, ’F‘, ’G‘, ’H’]
df = pd.read_csv(‘no_header.csv’, header=None, names=column_names)
```

## `read_table`函数
`read_table`函数与`read_csv`功能类似，主要区别在于默认分隔符：`read_csv`默认使用逗号，而`read_table`默认使用制表符（`\t`）。两者都可以通过`sep`参数指定任意分隔符。

```python
# 使用read_table读取制表符分隔的文件
df = pd.read_table(‘data.txt’)

# 指定分隔符，例如空格（也支持正则表达式）
df = pd.read_csv(‘data.txt’, sep=‘\s+’)
```

## 其他重要参数
以下是`read_csv`/`read_table`函数中其他一些有用的参数：

*   **`sep`**: 指定字段分隔符，默认为逗号。可以是字符串或正则表达式（如`\s+`匹配任意空白字符）。
*   **`header`**: 指定将文件第几行作为列名。设置为`None`表示文件无表头。
*   **`names`**: 当`header=None`时，用于手动指定列名列表。
*   **`index_col`**: 指定用作行索引的列编号或列名。
*   **`skiprows`**: 指定要跳过的行号列表（从0开始）。例如，`skiprows=[1,2,3]`会跳过第2、3、4行。
*   **`na_values`**: 指定哪些字符串应被解释为缺失值（NaN）。这对于处理非标准缺失值标记（如“N/A”、“NULL”、“-”）非常有用。
    ```python
    df = pd.read_csv(‘data.csv’, na_values=[‘N/A‘, ’NULL‘, ’-’])
    ```
*   **`parse_dates`**: 尝试将指定列解析为日期时间格式。可以是布尔值或列名/编号的列表。

![](img/791b7b6c00f1056b5787868e287d226b_4.png)

## 总结
本节课我们一起学习了Pandas中读取文件数据的关键技能。我们掌握了如何使用`read_csv`和`read_table`函数，并深入了解了如何通过参数控制索引列、解析日期、处理缺失值以及应对无表头文件等情况。熟练运用这些参数，能够帮助我们高效、准确地将外部数据加载到Pandas的DataFrame中，为后续的金融量化分析打下坚实的数据基础。