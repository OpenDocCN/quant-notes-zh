# Python金融量化：P11：10 股票数据预处理 📊

在本节课中，我们将学习如何使用Python进行股票数据的获取与预处理。我们将通过一个实际案例，巩固DataFrame的基础操作，并体验金融量化分析的初步流程。

上一节我们介绍了DataFrame的基础操作，本节中我们来看看如何将这些操作应用于真实的股票数据分析。

## 获取股票历史数据 📈

![](img/6a0328e37fed595f08b416dd7a460404_1.png)

![](img/6a0328e37fed595f08b416dd7a460404_3.png)

要进行股票分析，首先需要获取历史行情数据。我们使用`tushare`这个财经数据接口包来获取数据。

![](img/6a0328e37fed595f08b416dd7a460404_5.png)

![](img/6a0328e37fed595f08b416dd7a460404_7.png)

![](img/6a0328e37fed595f08b416dd7a460404_9.png)

![](img/6a0328e37fed595f08b416dd7a460404_11.png)

**核心步骤：**
1.  安装`tushare`包。
2.  导入必要的库。
3.  调用接口获取数据。

![](img/6a0328e37fed595f08b416dd7a460404_13.png)

![](img/6a0328e37fed595f08b416dd7a460404_15.png)

![](img/6a0328e37fed595f08b416dd7a460404_17.png)

以下是具体操作步骤：

![](img/6a0328e37fed595f08b416dd7a460404_19.png)

1.  **安装与导入模块**
    首先，需要安装`tushare`包，并导入`pandas`等数据分析库。
    ```python
    # 安装tushare (在命令行中执行)
    # pip install tushare

    # 导入模块
    import tushare as ts
    import pandas as pd
    from pandas import DataFrame, Series
    import numpy as np
    ```

![](img/6a0328e37fed595f08b416dd7a460404_21.png)

![](img/6a0328e37fed595f08b416dd7a460404_23.png)

2.  **获取股票数据**
    使用`tushare`的`get_k_data`函数可以获取指定股票代码在特定时间段的历史行情数据。
    ```python
    # 获取股票代码为‘600519’（贵州茅台）从较早时间到最近的历史数据
    df = ts.get_k_data(code='600519', start='2000-01-01')
    print(df.head()) # 查看前几行数据
    ```
    返回的`df`是一个DataFrame，包含日期(`date`)、开盘价(`open`)、收盘价(`close`)、最高价(`high`)、最低价(`low`)、成交量(`volume`)和股票代码(`code`)等列。

## 数据存储与读取 💾

为了数据稳定性和避免重复网络请求，可以将获取的数据保存到本地文件，后续直接从文件读取。

**核心步骤：**
1.  将DataFrame数据保存为CSV文件。
2.  从CSV文件读取数据到DataFrame。

以下是具体操作步骤：

1.  **保存数据到本地**
    使用DataFrame的`to_csv`方法将数据写入CSV文件。
    ```python
    # 将数据保存到名为‘maotai.csv’的文件中
    df.to_csv('./maotai.csv')
    ```

2.  **从本地读取数据**
    使用pandas的`read_csv`函数读取本地CSV文件。
    ```python
    # 读取本地CSV文件
    df = pd.read_csv('./maotai.csv')
    print(df.head())
    ```

## 数据清洗与预处理 🧹

从文件读取的数据可能包含无用信息或格式不正确，需要进行清洗和转换。

**核心步骤：**
1.  删除无用的列。
2.  查看数据类型。
3.  转换日期列为时间序列类型。
4.  设置日期为行索引。

以下是具体操作步骤：

1.  **删除无用列**
    读取数据后，DataFrame会自动生成一个表示行号的索引列（列名为`Unnamed: 0`），这列数据通常无用，可以删除。
    ```python
    # 删除名为‘Unnamed: 0’的列，axis=1表示对列操作，inplace=True表示直接修改原DataFrame
    df.drop(labels='Unnamed: 0', axis=1, inplace=True)
    print(df.head())
    ```

2.  **查看数据信息**
    使用`info()`方法可以快速查看DataFrame的概况，包括每列的数据类型和非空值数量。
    ```python
    # 查看DataFrame的详细信息
    print(df.info())
    ```
    通过输出可以发现，`date`列的数据类型是`object`（字符串），而不是时间类型。

3.  **转换日期格式**
    为了便于进行时间序列分析，需要将`date`列从字符串转换为`datetime`类型。
    ```python
    # 将‘date’列转换为datetime类型
    df['date'] = pd.to_datetime(df['date'])
    print(df.info()) # 再次查看，确认‘date’列类型已变为datetime64
    ```

4.  **设置日期为行索引**
    在时间序列分析中，常将日期作为索引。使用`set_index`方法可以将指定列设置为DataFrame的行索引。
    ```python
    # 将‘date’列设置为行索引
    df.set_index('date', inplace=True)
    print(df.head())
    ```
    设置完成后，`date`列不再作为普通列出现，而是成为了DataFrame的行索引。

![](img/6a0328e37fed595f08b416dd7a460404_25.png)

本节课中我们一起学习了股票数据预处理的完整流程：从使用`tushare`获取网络数据，到将数据存储到本地文件，再到读取文件并进行数据清洗（删除无用列、转换日期格式、设置索引）。这些步骤为后续的股票策略分析和量化建模打下了坚实的数据基础。下一节，我们将基于处理好的数据，实现一个简单的股票分析案例。