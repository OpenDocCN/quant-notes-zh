# Python金融量化投资分析与股票交易：P31：文件读取 📂

在本节课中，我们将学习如何使用Pandas库读取外部数据文件。这是数据分析的基础，因为真实世界的数据通常存储在文件中，而非手动输入。

## 概述

之前我们已经学习了Pandas的多种功能，包括灵活的数据操作、数据对齐、缺失值处理和时间序列分析。本节将介绍最后一部分核心内容：文件处理。我们将重点学习如何从CSV文件中读取数据，并掌握`read_csv`函数的关键参数。

## CSV文件格式简介

![](img/124002184f05cd24c93938c541998de9_1.png)

![](img/124002184f05cd24c93938c541998de9_2.png)

CSV（逗号分隔值）文件是一种常见的文本文件格式。文件中的每一行代表一条数据记录，每个记录中的字段由逗号分隔，相当于Excel表格中的一个单元格。

## 使用`read_csv`函数读取数据

`read_csv`函数是Pandas中用于读取CSV文件的主要工具。

以下是一个示例文件内容，它包含了某只股票从2007年3月1日到2017年11月10日的行情数据。数据列包括：序号、时间、开盘价、收盘价、最高价、最低价、成交量和股票代码。

```python
import pandas as pd
df = pd.read_csv(‘stock_data.csv’)
```

执行上述代码后，数据会被读取到一个DataFrame中。但初始读取结果可能存在几个需要调整的问题。

## 处理读取时的常见问题

上一节我们介绍了基本的读取方法，本节中我们来看看如何解决读取时遇到的具体问题。

### 1. 设置行索引

默认情况下，Pandas会生成一个从0开始的整数序列作为行索引。如果我们希望使用数据中的某一列作为索引，可以使用`index_col`参数。

以下是`index_col`参数的用法：
*   **传入列编号**：`index_col=0` 表示使用第一列作为索引。
*   **传入列名**：`index_col=‘date’` 表示使用名为‘date’的列作为索引。

```python
# 使用第一列作为索引
df = pd.read_csv(‘stock_data.csv’, index_col=0)
# 使用‘date’列作为索引
df = pd.read_csv(‘stock_data.csv’, index_col=‘date’)
```

### 2. 解析日期时间

即使将日期列设置为索引，Pandas默认会将其读取为字符串对象。为了进行时间序列分析，我们需要将其转换为`datetime`类型。

这可以通过`parse_dates`参数实现：
*   **传入布尔值**：`parse_dates=True` 会尝试解析所有可以解释为日期的列。
*   **传入列表**：`parse_dates=[‘date’]` 指定解析‘date’这一列。

```python
# 解析所有可能的日期列
df = pd.read_csv(‘stock_data.csv’, parse_dates=True)
# 仅解析‘date’列
df = pd.read_csv(‘stock_data.csv’, parse_dates=[‘date’])
```

### 3. 处理无列名的文件

如果CSV文件的第一行就是数据，没有列名，直接读取会导致第一行数据被误认为是列名。这时需要使用`header`和`names`参数。

以下是相关参数的用法：
*   `header=None`： 指定文件没有表头，Pandas会自动生成整数列名（0, 1, 2…）。
*   `names=[‘A‘, ’B‘, ’C‘]`： 在`header=None`的基础上，可以传入一个列表来自定义列名。

```python
# 文件无列名，自动生成列名
df = pd.read_csv(‘data_without_header.csv’, header=None)
# 文件无列名，并指定自定义列名
df = pd.read_csv(‘data_without_header.csv’, header=None, names=[‘col1‘, ’col2‘, ’col3‘])
```

### 4. `read_csv`与`read_table`

除了`read_csv`，Pandas还提供了`read_table`函数。两者的主要区别在于默认分隔符：
*   `read_csv`默认分隔符是逗号（`,`）。
*   `read_table`默认分隔符是制表符（`\t`）。

通过`sep`参数，可以指定任何字符或正则表达式作为分隔符。

```python
# 使用read_table以逗号分隔（效果同read_csv）
df = pd.read_table(‘stock_data.csv’, sep=‘,’)
# 使用正则表达式匹配任意空白字符作为分隔符
df = pd.read_csv(‘data.txt’, sep=‘\s+’)
```

### 5. 跳过指定行

在读取文件时，有时需要跳过开头的几行（如文件说明）。可以使用`skiprows`参数。

以下是`skiprows`参数的用法：
*   传入整数`n`：跳过文件开头的`n`行。
*   传入列表`[1, 3, 5]`：跳过指定的行号（0-based）。

```python
# 跳过文件的前3行
df = pd.read_csv(‘stock_data.csv’, skiprows=3)
# 跳过第2、4、6行（索引为1，3，5）
df = pd.read_csv(‘stock_data.csv’, skiprows=[1, 3, 5])
```

### 6. 识别自定义缺失值

数据中的缺失值可能被标记为各种字符串（如“N/A”、“NULL”、“-”）。如果这些标记未被识别，整列数据可能会被错误地推断为对象（字符串）类型。

使用`na_values`参数可以指定哪些字符串应被识别为缺失值（NaN）。

```python
# 将‘N/A‘和‘NULL‘识别为缺失值
df = pd.read_csv(‘stock_data.csv’, na_values=[‘N/A‘, ’NULL’])
# 针对特定列指定缺失值标识
df = pd.read_csv(‘stock_data.csv’, na_values={‘volume‘: [0], ’price‘: [‘N/A’]})
```

## 核心参数总结

本节课中我们一起学习了`read_csv`和`read_table`函数的核心参数，它们能帮助我们灵活地处理各种格式的数据文件。

以下是主要参数及其作用的总结：
*   `sep` / `delimiter`： 指定字段分隔符。
*   `header`： 指定哪一行作为列名（默认0），`None`表示无列名。
*   `names`： 当`header=None`时，用于指定列名列表。
*   `index_col`： 指定用作行索引的列编号或列名。
*   `skiprows`： 需要跳过的行号列表或行数。
*   `na_values`： 一组用于替换为`NaN`的字符串。
*   `parse_dates`： 尝试将数据解析为日期时间格式。

![](img/124002184f05cd24c93938c541998de9_4.png)

掌握这些参数，你就能从容地从绝大多数CSV或文本文件中导入所需的数据，为后续的金融量化分析打下坚实的基础。