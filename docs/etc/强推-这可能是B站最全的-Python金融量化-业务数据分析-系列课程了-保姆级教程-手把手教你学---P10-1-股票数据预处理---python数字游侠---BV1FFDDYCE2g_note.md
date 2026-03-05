# Python金融量化+业务数据分析：P10：1.股票数据预处理 📊

在本节课中，我们将学习如何使用Python进行股票数据的预处理。我们将通过一个实际的项目，巩固DataFrame的基础操作，特别是索引和切片，并初步体验金融量化分析的基本流程。

上一节我们介绍了DataFrame的基础操作，本节中我们来看看如何将这些操作应用于真实的股票数据预处理场景。

## 项目概述与需求

首先，我们明确本项目的目标。为了制定股票买卖策略，我们需要分析股票的历史交易数据。历史数据是公开透明的，可用于分析和预测未来趋势。

以下是本项目的具体需求：
1.  使用 `tushare` 包获取某只股票的历史行情数据。
2.  将获取的股票数据存储到本地文件。
3.  将本地存储的数据读入到DataFrame中。
4.  对读取的数据进行清洗和处理，包括删除无用列、转换数据类型和设置索引。

## 第一步：获取股票历史数据

首先，我们需要安装并导入必要的工具。`tushare` 是一个免费的Python财经数据接口包。

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_1.png)

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_3.png)

以下是安装和导入步骤的代码：

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_5.png)

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_7.png)

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_9.png)

```python
# 安装 tushare
# pip install tushare

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_11.png)

# 导入所需模块
import tushare as ts
import pandas as pd
from pandas import DataFrame, Series
import numpy as np
```

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_13.png)

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_15.png)

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_17.png)

接下来，我们使用 `tushare` 获取股票数据。这里以股票代码 `‘600519’`（贵州茅台）为例，获取其自上市以来到昨天的所有历史数据。

```python
# 获取股票历史数据
df = ts.get_k_data(code=‘600519‘, start=‘2000-01-01‘)
print(df)
```

执行上述代码后，我们将得到一个DataFrame，包含日期、开盘价、收盘价、最高价、最低价、成交量和股票代码等列。

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_19.png)

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_21.png)

## 第二步：数据存储与读取

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_23.png)

直接从网络获取数据可能不够稳定，因此我们将数据保存到本地。Pandas提供了便捷的方法将DataFrame写入文件。

以下是保存数据到CSV文件的代码：

```python
# 将数据保存到本地CSV文件
df.to_csv(‘maotai.csv‘)
```

数据保存后，我们可以随时从本地读取，避免重复网络请求。

以下是从CSV文件读取数据的代码：

```python
# 从本地CSV文件读取数据
df = pd.read_csv(‘maotai.csv‘)
print(df.head())
```

## 第三步：数据清洗与处理

读取数据后，我们需要进行清洗和格式化，使其更适合分析。

**1. 删除无用列**

读取的CSV文件通常会包含一个默认的索引列（`Unnamed: 0`），我们需要将其删除。

以下是删除指定列的代码：

```python
# 删除无用的索引列
df.drop(labels=‘Unnamed: 0‘, axis=1, inplace=True)
print(df.head())
```

**2. 查看数据类型**

在进行进一步操作前，最好先了解每一列的数据类型。`info()` 方法可以提供数据概览。

以下是查看数据信息的代码：

```python
# 查看DataFrame的信息和数据类型
print(df.info())
```

通过 `info()` 的输出，我们发现 `date` 列是对象（字符串）类型，而不是时间类型。

**3. 转换时间序列**

为了便于基于时间的分析，我们需要将 `date` 列转换为Pandas的日期时间类型。

以下是转换数据类型的代码：

```python
# 将‘date‘列转换为日期时间类型
df[‘date‘] = pd.to_datetime(df[‘date‘])
print(df.info())
```

转换后，`date` 列的类型变为 `datetime64`。

**4. 设置行索引**

在时间序列分析中，将日期设置为行索引非常有用。这可以通过 `set_index()` 方法实现。

以下是设置行索引的代码：

```python
# 将‘date‘列设置为行索引
df.set_index(‘date‘, inplace=True)
print(df.head())
```

设置完成后，`date` 列成为了DataFrame的行索引，数据看起来更加整洁，便于后续基于时间的切片和重采样操作。

## 总结

![](img/bc7c2f95eca25d2a68ecf12e391be5ae_25.png)

本节课中我们一起学习了股票数据预处理的完整流程。我们首先使用 `tushare` 包从网络获取股票历史数据，接着将数据存储到本地CSV文件以备后用。然后，我们将数据读入DataFrame，并进行了关键的数据清洗步骤：删除无用列、检查数据类型、将日期字符串转换为时间序列类型，最后将日期列设置为行索引。这些步骤为后续的金融量化分析（如指标计算、策略回测）打下了坚实的数据基础。下一节，我们将利用处理好的数据，实现一个简单的“双均线”交易策略。