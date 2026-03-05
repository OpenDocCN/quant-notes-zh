# 量化交易实战课程：P39：tushare包介绍 📈

![](img/6467b07c2cdecc217f833dd171e468e8_1.png)

![](img/6467b07c2cdecc217f833dd171e468e8_3.png)

## 概述
在本节课中，我们将学习如何使用 `tushare` 这个Python财经数据接口包。`tushare` 是一个免费开源的库，专门用于获取国内A股市场的股票数据。我们将重点介绍如何安装它，以及如何使用其核心接口来下载历史行情数据，为后续的金融分析做准备。

![](img/6467b07c2cdecc217f833dd171e468e8_5.png)

![](img/6467b07c2cdecc217f833dd171e468e8_7.png)

## tushare包简介
上一节我们介绍了NumPy、Pandas和Matplotlib这三大数据分析工具。本节中我们来看看如何获取实际的金融数据来进行分析。要进行金融分析，首先需要有数据来源。`tushare` 包就是一个专门用于获取中国股票市场数据的Python接口。

![](img/6467b07c2cdecc217f833dd171e468e8_9.png)

![](img/6467b07c2cdecc217f833dd171e468e8_11.png)

`tushare` 的数据来源于上海证券交易所、深圳证券交易所、新浪财经、腾讯财经等多个公开渠道。它将这些数据整合到自己的服务器上，用户通过调用其提供的Python函数，就能方便地将数据下载到本地进行分析。

![](img/6467b07c2cdecc217f833dd171e468e8_13.png)

![](img/6467b07c2cdecc217f833dd171e468e8_15.png)

## 安装与导入
以下是安装 `tushare` 的步骤。

![](img/6467b07c2cdecc217f833dd171e468e8_17.png)

![](img/6467b07c2cdecc217f833dd171e468e8_19.png)

![](img/6467b07c2cdecc217f833dd171e468e8_21.png)

```python
# 使用pip安装tushare
pip install tushare
```
安装过程中如果提示缺少某些依赖包，请根据提示逐一安装这些依赖包，然后再重新安装 `tushare`。

![](img/6467b07c2cdecc217f833dd171e468e8_23.png)

![](img/6467b07c2cdecc217f833dd171e468e8_25.png)

安装成功后，在Python中按以下方式导入，通常使用 `ts` 作为其简称。

```python
import tushare as ts
```

![](img/6467b07c2cdecc217f833dd171e468e8_27.png)

## 核心接口：获取历史行情数据
`tushare` 提供了多个数据接口，其中最常用的是获取股票历史行情数据的接口。我们将重点学习 `get_k_data()` 函数。

该函数的主要参数如下：
*   **code**：股票代码，例如 `‘601318’`。
*   **start**：数据开始日期，格式为 `‘YYYY-MM-DD’`。
*   **end**：数据结束日期，格式为 `‘YYYY-MM-DD’`。如果不指定，默认为当前日期。
*   **ktype**：K线类型。`‘D’` 代表日K线，`‘W’` 代表周K线，`‘M’` 代表月K线。默认为 `‘D’`。
*   **autype**：复权类型。`‘qfq’` 代表前复权，`‘hfq’` 代表后复权，`‘None’` 代表不复权。对于大多数分析，使用默认的前复权（`‘qfq’`）即可。
*   **index**：布尔值。当获取大盘指数数据（如上证指数`000001`）时，需要将此参数设置为 `True`。

![](img/6467b07c2cdecc217f833dd171e468e8_29.png)

### 基础使用示例
以下是一个获取单只股票日K线数据的代码示例。

![](img/6467b07c2cdecc217f833dd171e468e8_31.png)

![](img/6467b07c2cdecc217f833dd171e468e8_33.png)

```python
import tushare as ts

![](img/6467b07c2cdecc217f833dd171e468e8_35.png)

# 获取中国平安（601318）的历史日K线数据
df = ts.get_k_data(code=‘601318’)
print(df.head())
```
执行以上代码会返回一个Pandas的DataFrame，包含以下列：日期（date）、开盘价（open）、收盘价（close）、最高价（high）、最低价（low）、成交量（volume）和股票代码（code）。

![](img/6467b07c2cdecc217f833dd171e468e8_37.png)

![](img/6467b07c2cdecc217f833dd171e468e8_39.png)

### 指定时间范围与保存数据
默认情况下，`get_k_data()` 会返回最近几年的数据。如果想获取特定时间段或全部历史数据，需要使用 `start` 和 `end` 参数。

```python
# 获取中国平安从2007年至今的全部历史数据
df = ts.get_k_data(code=‘601318’, start=‘2007-01-01’)

# 将数据保存到本地的CSV文件中
df.to_csv(‘pingan_stock_data.csv’, index=False)
```
如果不知道股票具体的上市日期，可以将 `start` 设为一个足够早的日期（如 `‘1990-01-01’`），函数会自动从该股票上市首日开始返回数据。

![](img/6467b07c2cdecc217f833dd171e468e8_41.png)

![](img/6467b07c2cdecc217f833dd171e468e8_43.png)

![](img/6467b07c2cdecc217f833dd171e468e8_45.png)

### 获取大盘指数数据
获取大盘指数（如沪深300指数 `000300`）数据时，需要将 `index` 参数设置为 `True`。

![](img/6467b07c2cdecc217f833dd171e468e8_47.png)

```python
# 获取沪深300指数的历史数据
df_index = ts.get_k_data(code=‘000300’, index=True)
print(df_index.head())
```
指数数据的数值代表指数点位，而非股票价格。

![](img/6467b07c2cdecc217f833dd171e468e8_49.png)

## 其他数据接口简介
除了历史行情接口，`tushare` 还提供了其他类型的数据接口，例如：
*   **实时行情**：`get_today_all()` 可以获取所有股票当日的最新行情概览。
*   **分笔数据**：`get_tick_data()` 可以获取历史或实时的分笔成交明细。
*   **大盘指数**：`get_index()` 可以获取实时的大盘指数信息。

![](img/6467b07c2cdecc217f833dd171e468e8_51.png)

![](img/6467b07c2cdecc217f833dd171e468e8_53.png)

这些接口的用法类似，均有详细的参数说明。对于本课程的后续简单分析而言，掌握 `get_k_data()` 接口来下载并保存数据已经足够。

![](img/6467b07c2cdecc217f833dd171e468e8_55.png)

![](img/6467b07c2cdecc217f833dd171e468e8_57.png)

## 总结
本节课我们一起学习了 `tushare` 财经数据包。我们了解了它的作用和数据来源，学会了如何安装和导入。重点是掌握了使用 `get_k_data()` 接口下载股票历史行情数据的方法，包括如何指定股票代码、时间范围，以及如何区分股票数据和指数数据。最后，我们还学会了将获取到的DataFrame数据保存到CSV文件中，为下一阶段的金融数据分析准备好了数据基础。