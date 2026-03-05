# Python金融分析+量化交易：01：股票数据预处理 📊

在本节课中，我们将学习如何使用Python进行股票数据的获取与预处理。这是进行金融量化分析的第一步，我们将通过一个实际案例，巩固DataFrame的基础操作，并了解如何为后续的策略分析准备数据。

在上一节中，我们介绍了DataFrame的基础操作。本节中，我们将通过一个股票数据预处理的小项目来实践和巩固这些知识，特别是索引与切片操作。

## 课程内容概述

本课程将涉及金融量化相关的项目实战。本节将通过一个股票分析的初步案例，让大家感受如何使用数据分析进行金融量化，并了解需要掌握的核心技能。

## 项目需求与实现步骤

### 需求一：获取股票历史行情数据

![](img/cbcde0b1c742baf236d99e750d61e16c_1.png)

制定股票买卖策略需要分析股票的历史交易数据。未来的股票走势无法获取，我们只能基于公开透明的历史数据进行预判。

![](img/cbcde0b1c742baf236d99e750d61e16c_3.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_5.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_7.png)

第一个需求是使用 `tushare` 包获取某只股票的历史行情数据。

![](img/cbcde0b1c742baf236d99e750d61e16c_9.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_11.png)

#### 1. 安装与介绍Tushare

![](img/cbcde0b1c742baf236d99e750d61e16c_13.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_15.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_17.png)

`tushare` 是一个财经数据接口包，需要手动安装。

**安装命令：**
```bash
pip install tushare
```

`tushare` 是一个免费、开源的Python财经数据接口包。它能实现股票等金融数据从采集、清洗到存储的过程，为金融分析人员提供快速、整洁、多样的数据。

![](img/cbcde0b1c742baf236d99e750d61e16c_19.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_21.png)

![](img/cbcde0b1c742baf236d99e750d61e16c_23.png)

#### 2. 获取数据

导入必要的模块，并使用 `tushare` 获取数据。

**代码示例：**
```python
import tushare as ts
import pandas as pd
from pandas import DataFrame, Series
import numpy as np

# 获取股票‘600519’（贵州茅台）从较早时间到最近的历史数据
df = ts.get_k_data(code='600519', start='2000-01-01')
print(df.head())
```
调用 `get_k_data()` 方法，传入股票代码（`code`）和开始日期（`start`），即可获取该时间段内的历史行情数据。数据包含日期（`date`）、开盘价（`open`）、收盘价（`close`）、最高价（`high`）、最低价（`low`）、成交量（`volume`）和股票代码（`code`）等列。

#### 3. 将数据存储到本地

为了保证数据获取的稳定性，可以将从互联网获取的数据存储到本地。

**代码示例：**
```python
# 将DataFrame数据存储为CSV文件
df.to_csv('maotai.csv')
```
通过调用 `to_csv()` 方法，可以将DataFrame中的数据写入本地的CSV文件。

### 需求二：读取并预处理本地数据

接下来，我们将本地存储的数据读入DataFrame，并进行必要的清洗和转换。

#### 1. 读取本地数据

使用pandas的 `read_csv()` 函数读取CSV文件。

**代码示例：**
```python
df = pd.read_csv('maotai.csv')
print(df.head())
```

#### 2. 删除无用列

读取数据后，可能会发现多出一列无用的索引列，需要将其删除。

**代码示例：**
```python
# 删除名为‘Unnamed: 0’的列
df.drop(labels='Unnamed: 0', axis=1, inplace=True)
print(df.head())
```
在 `drop()` 函数中，`axis=1` 表示删除列，`inplace=True` 表示将修改直接应用到原DataFrame。

#### 3. 查看数据类型

了解每一列的数据类型对于后续处理至关重要。

**代码示例：**
```python
# 查看DataFrame的详细信息，包括每列数据类型和非空值数量
print(df.info())
```
`info()` 方法可以返回数据的概要信息，帮助我们判断是否存在空值以及各列的数据类型。

#### 4. 转换时间序列

数据中的日期列（`date`）默认是字符串类型，需要将其转换为时间序列类型以便于时间相关的分析。

**代码示例：**
```python
# 将‘date’列转换为datetime类型
df['date'] = pd.to_datetime(df['date'])
print(df.info())
```
转换后，`date` 列的数据类型会从 `object` 变为 `datetime64`。

#### 5. 设置时间索引

在时间序列分析中，将日期设置为行索引是常见做法。

**代码示例：**
```python
# 将‘date’列设置为行索引
df.set_index('date', inplace=True)
print(df.head())
```
设置完成后，`date` 列将不再是普通列，而是成为DataFrame的行索引。

## 总结

本节课中，我们一起学习了股票数据预处理的全流程：
1.  使用 `tushare` 包从网络获取股票历史行情数据。
2.  将数据保存到本地CSV文件。
3.  从本地读取数据到DataFrame。
4.  进行数据清洗，包括删除无用列、转换日期格式以及将日期列设置为索引。

![](img/cbcde0b1c742baf236d99e750d61e16c_25.png)

这些步骤为后续的金融量化分析（例如下一节将介绍的双均线策略）打下了坚实的数据基础。通过这个案例，我们不仅巩固了DataFrame的索引、切片等基础操作，也初步体验了数据分析在金融领域的应用方式。