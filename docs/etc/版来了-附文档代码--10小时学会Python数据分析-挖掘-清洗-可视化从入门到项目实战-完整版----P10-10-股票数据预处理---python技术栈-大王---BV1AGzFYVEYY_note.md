# Python金融量化：P10：股票数据预处理 📊

在本节课中，我们将学习如何使用Python进行股票数据的获取与预处理。我们将通过一个实际案例，巩固DataFrame的基础操作，并初步体验金融量化分析的基本流程。

## 概述
上一节我们介绍了DataFrame的基础操作。本节中，我们将通过一个股票数据分析的小项目，来实践这些操作，特别是索引与切片的应用。我们将学习如何获取股票历史数据、进行数据清洗与转换，为后续的分析策略制定打下基础。

![](img/92d05919ce73c69c9239c378ce420fa1_1.png)

![](img/92d05919ce73c69c9239c378ce420fa1_3.png)

![](img/92d05919ce73c69c9239c378ce420fa1_5.png)

![](img/92d05919ce73c69c9239c378ce420fa1_7.png)

![](img/92d05919ce73c69c9239c378ce420fa1_9.png)

![](img/92d05919ce73c69c9239c378ce420fa1_11.png)

## 数据获取与存储
首先，我们需要获取股票的历史行情数据。这里我们使用`tushare`这个财经数据接口包。

![](img/92d05919ce73c69c9239c378ce420fa1_13.png)

![](img/92d05919ce73c69c9239c378ce420fa1_15.png)

![](img/92d05919ce73c69c9239c378ce420fa1_17.png)

![](img/92d05919ce73c69c9239c378ce420fa1_19.png)

![](img/92d05919ce73c69c9239c378ce420fa1_21.png)

以下是安装与导入步骤：
```python
# 安装tushare包
# pip install tushare

![](img/92d05919ce73c69c9239c378ce420fa1_23.png)

![](img/92d05919ce73c69c9239c378ce420fa1_25.png)

# 导入必要的库
import tushare as ts
import pandas as pd
from pandas import DataFrame, Series
import numpy as np
```

![](img/92d05919ce73c69c9239c378ce420fa1_27.png)

使用`tushare`获取某只股票（例如代码为`600519`的贵州茅台）的历史数据：
```python
# 获取股票历史数据
df = ts.get_k_data(code='600519', start='2000-01-01')
print(df.head())
```
这段代码会返回一个DataFrame，包含该股票从上市首日到最近一个交易日的数据，列包括日期(`date`)、开盘价(`open`)、收盘价(`close`)、最高价(`high`)、最低价(`low`)、成交量(`volume`)和股票代码(`code`)。

为了后续分析的稳定与便捷，我们将数据存储到本地：
```python
# 将数据存储到本地CSV文件
df.to_csv('maotai.csv', index=False)
```

## 数据读取与初步处理
接下来，我们将本地存储的数据读入DataFrame，并进行初步清洗。

读取数据：
```python
# 从本地CSV文件读取数据
df = pd.read_csv('maotai.csv')
print(df.head())
```

读取后，我们发现数据中多出了一列无用的索引列，需要将其删除：
```python
# 删除无用的列（假设列名为‘Unnamed: 0’）
df.drop(labels='Unnamed: 0', axis=1, inplace=True)
print(df.head())
```

## 数据类型检查与转换
在处理时间序列数据时，确保日期列是正确的时间类型至关重要。

首先，查看各列的数据类型：
```python
# 查看DataFrame的信息，包括列数据类型
print(df.info())
```
输出显示`date`列是`object`（字符串）类型，我们需要将其转换为时间序列类型。

进行数据类型转换：
```python
# 将‘date’列转换为datetime类型
df['date'] = pd.to_datetime(df['date'])
print(df.info())
```
现在，`date`列的类型已变为`datetime64`。

## 设置时间索引
在时间序列分析中，将日期设置为行索引是常见操作，便于后续基于时间的切片和重采样。

以下是设置索引的步骤：
```python
# 将‘date’列设置为行索引
df.set_index('date', inplace=True)
print(df.head())
```
现在，`date`列已成为DataFrame的行索引，数据已准备好用于进一步分析。

![](img/92d05919ce73c69c9239c378ce420fa1_29.png)

## 总结
本节课我们一起学习了股票数据的预处理流程。我们首先使用`tushare`包获取了股票历史数据，并将其存储到本地。然后，我们读取数据，进行了删除无用列、检查数据类型、将日期字符串转换为时间序列，以及设置时间索引等操作。这些步骤是进行金融量化分析前必不可少的数据准备阶段，为后续制定交易策略奠定了数据基础。