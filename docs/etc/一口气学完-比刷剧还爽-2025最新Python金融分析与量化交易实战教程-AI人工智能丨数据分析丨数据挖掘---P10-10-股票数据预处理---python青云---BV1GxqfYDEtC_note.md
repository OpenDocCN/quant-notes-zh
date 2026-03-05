# Python金融量化与股票交易：P10：股票数据预处理 📊

在本节课中，我们将学习如何使用Python进行股票数据的获取与预处理。这是进行金融量化分析和制定交易策略的基础步骤。我们将使用`tushare`财经数据接口包获取股票历史行情数据，并利用`pandas`库对数据进行清洗和整理，为后续的分析工作做好准备。

## 数据获取与本地存储 📥

![](img/4c0c8e862d585423d676eda07c0845d4_1.png)

上一节我们介绍了`DataFrame`的基础操作，本节中我们来看看如何获取真实的股票数据并进行初步处理。

![](img/4c0c8e862d585423d676eda07c0845d4_3.png)

![](img/4c0c8e862d585423d676eda07c0845d4_5.png)

![](img/4c0c8e862d585423d676eda07c0845d4_7.png)

![](img/4c0c8e862d585423d676eda07c0845d4_9.png)

![](img/4c0c8e862d585423d676eda07c0845d4_11.png)

![](img/4c0c8e862d585423d676eda07c0845d4_13.png)

首先，我们需要获取某只股票的历史行情数据。为此，我们将使用`tushare`这个免费的Python财经数据接口包。它能够为我们提供股票等金融产品的历史数据。

![](img/4c0c8e862d585423d676eda07c0845d4_15.png)

![](img/4c0c8e862d585423d676eda07c0845d4_17.png)

![](img/4c0c8e862d585423d676eda07c0845d4_19.png)

![](img/4c0c8e862d585423d676eda07c0845d4_21.png)

以下是获取数据并存储到本地的步骤：

![](img/4c0c8e862d585423d676eda07c0845d4_23.png)

![](img/4c0c8e862d585423d676eda07c0845d4_25.png)

1.  **安装与导入模块**：首先需要安装`tushare`包，并导入必要的库。
    ```python
    # 安装 tushare
    # pip install tushare

    # 导入模块
    import tushare as ts
    import pandas as pd
    from pandas import DataFrame, Series
    ```

![](img/4c0c8e862d585423d676eda07c0845d4_27.png)

2.  **获取股票历史数据**：使用`tushare`的`get_k_data`方法，传入股票代码和时间范围来获取数据。
    ```python
    # 获取股票‘600519’（贵州茅台）从较早时间到最近的历史数据
    df = ts.get_k_data(code=‘600519‘, start=‘2000-01-01‘)
    print(df.head())
    ```
    该方法返回一个`DataFrame`，包含日期(`date`)、开盘价(`open`)、收盘价(`close`)、最高价(`high`)、最低价(`low`)、成交量(`volume`)和股票代码(`code`)等列。

3.  **将数据存储到本地**：为了避免每次从网络获取数据，我们可以将数据保存到本地CSV文件。
    ```python
    # 将DataFrame中的数据写入到本地CSV文件
    df.to_csv(‘maotai.csv‘)
    ```

## 数据读取与初步清洗 🧹

数据存储到本地后，我们可以随时读取并进行处理。以下是读取数据并进行初步清洗的步骤：

1.  **从本地读取数据**：使用`pandas`的`read_csv`函数读取刚才保存的CSV文件。
    ```python
    # 读取本地CSV文件到DataFrame
    df = pd.read_csv(‘maotai.csv‘)
    print(df.head())
    ```

2.  **删除无用列**：读取的数据通常会包含一个无用的索引列，我们需要将其删除。
    ```python
    # 删除名为‘Unnamed: 0‘的列（轴向axis=1表示列，inplace=True表示直接修改原数据）
    df.drop(labels=‘Unnamed: 0‘, axis=1, inplace=True)
    print(df.head())
    ```

3.  **查看数据信息**：使用`info()`方法查看数据的整体信息，包括每列的数据类型和非空值数量。
    ```python
    # 查看DataFrame的详细信息
    print(df.info())
    ```
    通过输出，我们可以发现`date`列的数据类型是`object`（字符串），而不是时间类型。

## 数据类型转换与索引设置 ⚙️

为了便于进行时间序列分析，我们需要将日期列转换为正确的时间格式，并设置为数据的索引。

1.  **转换日期列数据类型**：将`date`列从字符串类型转换为`datetime`时间序列类型。
    ```python
    # 将‘date‘列转换为datetime类型
    df[‘date‘] = pd.to_datetime(df[‘date‘])
    # 再次查看数据类型确认转换成功
    print(df.info())
    ```

2.  **设置日期为行索引**：在时间序列分析中，将日期作为行索引是非常方便的操作。
    ```python
    # 将‘date‘列设置为DataFrame的行索引
    df.set_index(‘date‘, inplace=True)
    print(df.head())
    ```
    设置完成后，`date`列变成了数据的行索引，其他列（开盘价、收盘价等）则作为数据列保留。

## 总结 📝

![](img/4c0c8e862d585423d676eda07c0845d4_29.png)

本节课中我们一起学习了股票数据预处理的全过程。我们首先使用`tushare`包从网络获取了指定股票的历史行情数据，并将其存储到本地CSV文件以备后用。接着，我们读取本地数据，删除了多余的无用列，并检查了数据的基本信息。最后，我们完成了两个关键操作：将日期字符串转换为时间序列类型，以及将日期列设置为数据的行索引。这些步骤为后续进行更深入的金融量化分析（如计算指标、制定交易策略）打下了坚实的数据基础。