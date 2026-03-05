# 量化回测入门：P1：一行代码实现专业量化回测图表 📊

## 概述
在本节课中，我们将学习如何使用一个名为 `pyfolio` 的 Python 库，仅用一行代码生成专业的量化回测结果图表和指标。这对于量化投资初学者来说，是一个快速评估策略表现的高效工具。

## 背景介绍
上一节我们介绍了量化回测的基本概念。本节中，我们来看看一个能极大简化回测分析流程的工具。在量化投资领域，早期有一个名为 Quantopian 的公司，它通过开源数据和共享策略的模式推动了量化交易的普及。虽然该公司后来因商业模式问题而停止运营，但它留下的开源工具至今仍有很大价值。`pyfolio` 便是其核心工具之一，专门用于投资组合和策略的性能分析。

## 准备工作
在开始编写代码之前，我们需要准备好 Python 环境和必要的库。

以下是安装和引入所需库的步骤：
1.  安装 `pyfolio` 库。由于原版维护可能不及时，我们可以使用一个由社区维护的版本。在命令行中执行：`pip install pyfolio-reloaded`。
2.  引入数据分析库 `pandas` 和一个数据获取工具 `tushare`。`tushare` 提供了便捷的金融数据接口。

对应的代码如下：
```python
import pyfolio as pf
import pandas as pd
import tushare as ts
```

## 获取数据
准备好库之后，我们需要获取用于回测的股票数据。

以下是获取股票历史数据的函数：
```python
# 设置 tushare 的 token（需在 tushare 官网注册获取）
ts.set_token('your_token_here')
pro = ts.pro_api()

def fetch_stock_data(ts_code, start_date, end_date):
    """
    获取单只股票的历史行情数据
    参数:
        ts_code: 股票代码，例如 ‘000001.SZ’
        start_date: 开始日期，格式 ‘YYYYMMDD’
        end_date: 结束日期，格式 ‘YYYYMMDD’
    """
    df = pro.daily(ts_code=ts_code, start_date=start_date, end_date=end_date)
    df['trade_date'] = pd.to_datetime(df['trade_date'])
    df.set_index('trade_date', inplace=True)
    df.sort_index(inplace=True)
    return df
```
调用这个函数，例如 `fetch_stock_data(‘000001.SZ’， ‘20190101’， ‘20191231’)`，即可获得某只股票在2019年的日线数据。

## 计算收益率
获得数据后，我们需要计算投资这只股票的每日收益率。这里我们演示一个最简单的策略：买入并持有。

假设我们一直持有该股票，那么其每日价格变动率就是我们的日收益率。我们可以使用 `pandas` 的 `pct_change()` 方法轻松计算：
```python
# 假设 df 是上面函数获取的数据框
daily_returns = df[‘close’].pct_change().dropna()
```
这个 `daily_returns` 序列，就是我们要进行回测分析的核心输入。

![](img/82eb43f5d8bd1e41122fca051ff8b32f_1.png)

## 一行代码生成回测报告
现在，我们来到了本节课的核心部分。有了收益率数据，生成完整的回测报告只需一行代码。

![](img/82eb43f5d8bd1e41122fca051ff8b32f_3.png)

将收益率序列传入 `pyfolio` 的 `create_simple_tear_sheet` 函数：
```python
pf.create_simple_tear_sheet(daily_returns)
```
执行这行代码，`pyfolio` 会自动生成一个包含丰富信息的报告。报告会展示以下内容：
*   **收益概览**：年化收益率、累计收益率等。
*   **风险指标**：波动率、夏普比率、最大回撤等。
*   **图表**：资金曲线、月度收益热力图、回撤周期图等。

## 进阶应用
上一节我们演示了无基准的简单回测。本节中我们来看看如何与策略收益率进行比较。

在实际应用中，我们通常需要将自己的策略收益率与某个基准（如大盘指数）的收益率进行对比。`pyfolio` 同样可以轻松实现。以下是基本思路：
1.  计算你的策略产生的每日收益率序列 `strategy_returns`。
2.  计算基准（例如沪深300指数）的每日收益率序列 `benchmark_returns`。
3.  调用函数时传入这两个序列：
    ```python
    pf.create_simple_tear_sheet(returns=strategy_returns, benchmark_rets=benchmark_returns)
    ```
这样，生成的图表就会同时展示策略和基准的表现，便于直观比较。

![](img/82eb43f5d8bd1e41122fca051ff8b32f_5.png)

## 总结
本节课中我们一起学习了如何使用 `pyfolio` 库快速进行量化回测分析。我们从安装库、获取数据开始，到计算收益率，最后用一行核心代码 `pf.create_simple_tear_sheet(returns)` 生成了包含详细指标和图表的专业回测报告。这个方法极大地简化了量化分析初期的工作量，让初学者能更专注于策略逻辑本身。在后续的学习中，你可以尝试将自己的交易策略转换成收益率序列，并利用此工具进行快速评估和优化。