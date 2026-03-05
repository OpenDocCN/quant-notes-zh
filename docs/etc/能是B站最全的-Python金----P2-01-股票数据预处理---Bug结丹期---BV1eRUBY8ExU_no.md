# Python金融分析+量化交易：01：股票数据预处理 📊

在本节课中，我们将学习如何使用Python进行股票数据的获取与预处理。这是进行金融量化分析的第一步，我们将通过一个实际案例，巩固DataFrame的基础操作，并了解如何为后续的策略分析准备数据。

## 概述

上一节我们介绍了DataFrame的基础操作。本节中，我们将通过一个股票数据预处理的实战项目，来巩固索引、切片等操作，并初步感受金融量化分析的数据准备流程。

![](img/aea74de897664303f56011c7bcbc5d71_1.png)

![](img/aea74de897664303f56011c7bcbc5d71_3.png)

![](img/aea74de897664303f56011c7bcbc5d71_5.png)

## 项目需求与工具介绍

![](img/aea74de897664303f56011c7bcbc5d71_7.png)

![](img/aea74de897664303f56011c7bcbc5d71_9.png)

![](img/aea74de897664303f56011c7bcbc5d71_11.png)

![](img/aea74de897664303f56011c7bcbc5d71_13.png)

首先，我们需要获取某只股票的历史行情数据进行分析。因为未来的股票走势无法预知，我们只能基于公开、透明的历史交易数据来制定策略。

![](img/aea74de897664303f56011c7bcbc5d71_15.png)

![](img/aea74de897664303f56011c7bcbc5d71_17.png)

![](img/aea74de897664303f56011c7bcbc5d71_19.png)

![](img/aea74de897664303f56011c7bcbc5d71_21.png)

我们将使用 **Tushare** 这个财经数据接口包来获取数据。Tushare是一个免费、开源的Python财经数据接口包，能够为金融分析人员提供快速、整洁、多样的便于分析的数据。

以下是使用前的准备工作：

![](img/aea74de897664303f56011c7bcbc5d71_23.png)

![](img/aea74de897664303f56011c7bcbc5d71_25.png)

![](img/aea74de897664303f56011c7bcbc5d71_27.png)

1.  **安装Tushare包**：
    ```bash
    pip install tushare
    ```
2.  **导入必要的库**：
    ```python
    import tushare as ts
    import pandas as pd
    from pandas import DataFrame, Series
    import numpy as np
    ```

## 数据获取与本地存储

获取数据后，为了分析的稳定性，我们通常会将数据存储到本地。

以下是具体步骤：

1.  **获取股票历史数据**：
    我们使用 `ts.get_k_data()` 函数，传入股票代码（例如‘600519’代表贵州茅台）来获取数据。`start` 参数可以指定一个较早的日期以获取全部历史数据。
    ```python
    # 获取贵州茅台从上市到昨天的历史数据
    df = ts.get_k_data(code=‘600519‘, start=‘2000-01-01‘)
    print(df.head())
    ```
    返回的DataFrame包含以下列：`date`（日期）， `open`（开盘价）， `close`（收盘价）， `high`（最高价）， `low`（最低价）， `volume`（成交量）， `code`（股票代码）。

2.  **将数据存储到本地**：
    使用DataFrame的 `to_csv()` 方法将数据保存为CSV文件。
    ```python
    df.to_csv(‘maotai.csv‘)
    ```

## 数据读取与初步处理

接下来，我们将本地存储的数据读入，并进行清洗和格式化，使其更适合分析。

以下是数据处理的关键步骤：

1.  **从本地读取数据**：
    ```python
    df = pd.read_csv(‘maotai.csv‘)
    print(df.head())
    ```

2.  **删除无用列**：
    读取的CSV文件通常会多出一列无意义的索引列（列名为‘Unnamed: 0‘），我们需要将其删除。使用 `drop()` 方法，并指定 `axis=1` 表示删除列，`inplace=True` 表示直接修改原DataFrame。
    ```python
    df.drop(labels=‘Unnamed: 0‘, axis=1, inplace=True)
    print(df.head())
    ```

3.  **查看数据信息与类型**：
    使用 `info()` 方法可以快速查看各列的数据类型和非空值数量，这对了解数据质量很有帮助。
    ```python
    print(df.info())
    ```
    通过输出可以发现，`date` 列是对象（`object`）类型，即字符串，而不是时间类型。

4.  **转换日期列数据类型**：
    为了便于进行时间序列分析，我们需要将 `date` 列转换为Pandas的 `datetime` 类型。
    ```python
    df[‘date‘] = pd.to_datetime(df[‘date‘])
    print(df.info()) # 再次查看，date列类型已变为datetime64
    ```

5.  **设置日期为行索引**：
    在时间序列分析中，将日期设置为行索引是标准做法，便于按时间进行切片和重采样。
    ```python
    df.set_index(keys=‘date‘, inplace=True)
    print(df.head())
    ```
    完成此步骤后，`date` 列变成了DataFrame的行索引，数据格式已经为后续的分析做好了准备。

## 总结

![](img/aea74de897664303f56011c7bcbc5d71_29.png)

本节课中，我们一起学习了股票数据预处理的全流程。我们首先使用Tushare包获取了股票历史数据并存储到本地，然后读取数据，进行了删除冗余列、转换日期格式、设置时间索引等清洗操作。这些步骤是进行任何金融量化分析前必不可少的数据准备工作，为我们后续制定交易策略打下了坚实的基础。下一节，我们将利用处理好的数据，实现一个简单的“双均线”交易策略。