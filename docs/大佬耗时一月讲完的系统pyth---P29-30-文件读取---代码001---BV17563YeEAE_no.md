# 量化交易教程：P29：文件读取

## 概述
在本节课中，我们将学习如何使用Pandas库读取外部文件数据。这是数据分析与量化交易实战中至关重要的一步，因为真实的数据通常存储在文件中，而非手动输入。

![](img/f432b9b4432ad01de6cb8085483f83be_1.png)

## 文件读取基础
上一节我们介绍了Pandas在数据处理、对齐、缺失值处理和时间序列等方面的功能。本节中，我们来看看如何从文件中读取数据。

![](img/f432b9b4432ad01de6cb8085483f83be_2.png)

最常用的数据文件格式是CSV。CSV文件本质上是文本文件，其数据以逗号分隔，每一行代表一条记录，每个逗号之间的内容相当于Excel表格中的一个单元格。

Pandas提供了 `read_csv` 函数来读取CSV文件的内容。

```python
import pandas as pd
df = pd.read_csv(‘filename.csv’)
```

## 读取股票数据示例
以下是一个读取股票行情数据CSV文件的例子。该文件包含了某只股票从2007年3月1日到2017年11月10日的数据，列包括序号、时间、开盘价、收盘价、最高价、最低价、成交量和股票代码。

直接使用 `read_csv` 读取后，我们发现了一些问题：
1.  文件自带的序号列（0,1,2…）被读取为一列数据，同时Pandas又自动生成了一列新的行索引。
2.  我们可能希望将“日期”列设置为行索引。
3.  “日期”列被读取为字符串，而非时间序列对象。

## 关键参数详解
为了解决上述问题，我们需要了解 `read_csv` 函数的一些关键参数。

### 1. 设置行索引 (`index_col`)
`index_col` 参数用于指定将文件中的哪一列作为DataFrame的行索引。可以传入列的位置编号（从0开始）或列名。

```python
# 将第一列（位置0）作为索引
df = pd.read_csv(‘data.csv’, index_col=0)
# 将名为‘date’的列作为索引
df = pd.read_csv(‘data.csv’, index_col=‘date’)
```

### 2. 解析日期 (`parse_dates`)
`parse_dates` 参数可以将指定列解析为时间序列对象（`datetime`类型）。这有助于后续进行时间序列分析。

```python
# 将所有可以解析为日期的列都进行转换
df = pd.read_csv(‘data.csv’, parse_dates=True)
# 仅解析指定的列（例如‘date’列）
df = pd.read_csv(‘data.csv’, parse_dates=[‘date’])
```

### 3. 处理无列名文件 (`header`, `names`)
如果CSV文件没有表头（即第一行就是数据），Pandas默认会将第一行数据当作列名。通过 `header` 和 `names` 参数可以处理这种情况。

```python
# 文件无表头，不将第一行解释为列名，自动生成0,1,2…作为列名
df = pd.read_csv(‘data.csv’, header=None)
# 文件无表头，并自定义列名
df = pd.read_csv(‘data.csv’, header=None, names=[‘A’, ‘B’, ‘C’, ‘D’, ‘E’, ‘F’, ‘G’, ‘H’])
```

### 4. 通用表格读取 (`read_table`)
`read_table` 函数与 `read_csv` 功能类似，主要区别在于默认分隔符：`read_csv` 默认为逗号，而 `read_table` 默认为制表符（`\t`）。两者都可以通过 `sep` 参数指定任意分隔符。

```python
# 使用read_table读取以制表符分隔的文件
df = pd.read_table(‘data.txt’)
# 指定分隔符，例如空格（也支持正则表达式，如‘\s+’匹配任意空白符）
df = pd.read_csv(‘data.csv’, sep=‘ ’)
```

### 5. 识别缺失值 (`na_values`)
数据中的缺失值可能以不同形式存在（如“NaN”、“NA”、“-”等）。`na_values` 参数可以指定哪些字符串应被识别为缺失值（NaN）。

```python
# 将字符串‘N/A’和‘missing’识别为缺失值
df = pd.read_csv(‘data.csv’, na_values=[‘N/A’, ‘missing’])
```

### 6. 跳过指定行 (`skiprows`)
`skiprows` 参数允许在读取文件时跳过指定的行。可以传入一个行号列表。

```python
# 跳过文件的前3行（行号0,1,2）
df = pd.read_csv(‘data.csv’, skiprows=[0,1,2])
```

## 总结
本节课我们一起学习了Pandas中读取文件的核心函数 `read_csv` 和 `read_table`。我们重点掌握了以下几个关键参数的使用：
*   `index_col`：指定行索引列。
*   `parse_dates`：将数据解析为时间序列。
*   `header` / `names`：处理无列名文件或自定义列名。
*   `sep`：指定列分隔符。
*   `na_values`：自定义缺失值标识符。
*   `skiprows`：跳过文件中的指定行。

![](img/f432b9b4432ad01de6cb8085483f83be_4.png)

熟练掌握这些参数，能够帮助我们灵活、准确地将各种格式的外部数据加载到Pandas中进行后续分析，这是进行金融数据分析与量化交易建模的基础。