# Python金融量化：P28：tushare包介绍 📈

![](img/d4dff61de0f7812a45833a44449f8fcf_1.png)

在本节课中，我们将学习如何使用 `tushare` 包来获取金融数据。`tushare` 是一个免费开源的Python财经数据接口包，主要用于获取国内A股市场的股票数据。我们将介绍其基本功能、安装方法以及如何获取历史行情数据，为后续的金融分析打下数据基础。

![](img/d4dff61de0f7812a45833a44449f8fcf_3.png)

## 什么是tushare包？

![](img/d4dff61de0f7812a45833a44449f8fcf_5.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_7.png)

上一节我们介绍了NumPy、Pandas和Matplotlib这三大数据分析工具。本节中我们来看看如何获取真实的金融数据进行分析。要进行金融分析，首先需要数据。`tushare` 包就是一个专门用于获取国内股票数据的工具。

![](img/d4dff61de0f7812a45833a44449f8fcf_9.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_11.png)

`tushare` 是一个免费开源的Python财经数据接口包。其数据来源于上海证券交易所、新浪财经、腾讯财经、凤凰财经等多个公开渠道。`tushare` 将这些数据整合到自己的服务器中，用户通过安装该包并调用其提供的函数接口，即可方便地将数据下载到本地进行分析。

![](img/d4dff61de0f7812a45833a44449f8fcf_13.png)

## 如何安装tushare？

![](img/d4dff61de0f7812a45833a44449f8fcf_15.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_17.png)

安装 `tushare` 包非常简单，通常使用 `pip` 命令即可。

![](img/d4dff61de0f7812a45833a44449f8fcf_19.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_21.png)

以下是安装步骤：
*   使用命令 `pip install tushare` 进行安装。
*   在安装过程中，如果提示缺少某些依赖包，请根据提示信息安装相应的依赖包，然后再重新安装 `tushare`。

![](img/d4dff61de0f7812a45833a44449f8fcf_23.png)

## 如何使用tushare获取数据？

![](img/d4dff61de0f7812a45833a44449f8fcf_25.png)

安装完成后，我们首先需要导入 `tushare` 包。为了编写方便，通常为其设置一个简短的别名，例如 `ts`。

```python
import tushare as ts
```

![](img/d4dff61de0f7812a45833a44449f8fcf_27.png)

`tushare` 提供了多个接口函数来获取不同类型的数据，其中最常用的是获取股票历史行情数据的接口。

### 获取历史行情数据

获取历史行情数据主要使用 `get_k_data()` 函数。这个函数可以获取股票的K线图数据。

以下是 `get_k_data()` 函数的主要参数说明：
*   **code**：股票代码，例如 `‘601318’`。
*   **start**：数据开始日期，格式为 `‘YYYY-MM-DD’`。
*   **end**：数据结束日期，格式为 `‘YYYY-MM-DD’`。如果不设置，默认为当前日期。
*   **ktype**：K线类型。`‘D’` 代表日K线，`‘W’` 代表周K线，`‘M’` 代表月K线。默认是 `‘D’`。
*   **autype**：复权类型。`‘qfq’` 代表前复权。对于初学者，默认使用 `‘qfq’` 即可。
*   **index**：布尔值。当设置为 `True` 时，`code` 参数应传入指数代码（如 `‘000001’` 代表上证指数），用于获取大盘指数数据。默认是 `False`。

![](img/d4dff61de0f7812a45833a44449f8fcf_29.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_31.png)

让我们通过一个例子来获取中国平安（股票代码：601318）的历史数据。

![](img/d4dff61de0f7812a45833a44449f8fcf_33.png)

```python
# 获取股票‘601318’从2015年至今的日K线数据
df = ts.get_k_data(‘601318‘)
print(df.head())
```

执行上述代码后，会返回一个Pandas的DataFrame对象，包含以下列：日期（date）、开盘价（open）、收盘价（close）、最高价（high）、最低价（low）、成交量（volume）和股票代码（code）。

![](img/d4dff61de0f7812a45833a44449f8fcf_35.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_37.png)

### 保存数据到本地文件

![](img/d4dff61de0f7812a45833a44449f8fcf_39.png)

获取到数据后，我们可以将其保存到CSV文件中，方便后续反复使用和分析。

```python
# 获取‘601318‘的全部历史数据（通过设置一个很早的起始日期）
df = ts.get_k_data(‘601318‘, start=‘1988-01-01‘)
# 将数据保存到名为‘601318.csv‘的文件中
df.to_csv(‘601318.csv‘, index=False)
```

### 获取指数数据

![](img/d4dff61de0f7812a45833a44449f8fcf_41.png)

如果需要获取大盘指数数据，例如上证指数（代码：000001），需要将 `index` 参数设置为 `True`。

![](img/d4dff61de0f7812a45833a44449f8fcf_43.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_45.png)

```python
# 获取上证指数（000001）的历史数据
index_df = ts.get_k_data(‘000001‘, index=True)
print(index_df.head())
```

![](img/d4dff61de0f7812a45833a44449f8fcf_47.png)

## tushare的其他功能

![](img/d4dff61de0f7812a45833a44449f8fcf_49.png)

除了历史行情数据，`tushare` 还提供了其他多种数据接口。

以下是部分其他常用接口简介：
*   **实时行情**：`get_today_all()`，获取所有股票的今日实时行情。
*   **分笔数据**：`get_tick_data()`，获取历史或实时的分笔交易数据。
*   **大盘指数**：`get_index()`，获取实时大盘指数信息。

![](img/d4dff61de0f7812a45833a44449f8fcf_51.png)

这些接口的详细用法可以参考 `tushare` 的官方文档。对于本课程后续的简单分析示例，掌握 `get_k_data()` 函数来获取和保存股票历史数据已经足够。

![](img/d4dff61de0f7812a45833a44449f8fcf_53.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_55.png)

## 总结

![](img/d4dff61de0f7812a45833a44449f8fcf_57.png)

![](img/d4dff61de0f7812a45833a44449f8fcf_59.png)

本节课中我们一起学习了 `tushare` 财经数据接口包。我们了解了它的基本用途、安装方法，并重点学习了如何使用 `get_k_data()` 函数获取个股的历史行情数据和指数数据，以及如何将数据保存到本地CSV文件。有了真实、规整的金融数据，我们就可以在接下来的课程中，运用之前学到的NumPy、Pandas和Matplotlib技能进行实际的金融数据分析了。