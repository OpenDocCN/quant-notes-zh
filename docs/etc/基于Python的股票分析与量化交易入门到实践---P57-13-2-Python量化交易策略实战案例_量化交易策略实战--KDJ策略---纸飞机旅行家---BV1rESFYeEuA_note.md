# 基于Python的股票分析与量化交易入门到实践 - P57：13.2 Python量化交易策略实战案例_量化交易策略实战--KDJ策略

在本节课中，我们将学习如何实现一个基于KDJ指标的量化交易策略。我们将从策略原理讲起，逐步深入到代码实现，并通过回测来验证策略效果。

## 🧠 KDJ量化交易策略简介

上一节我们介绍了双均线策略的实战，本节中我们来看看KDJ策略。

KDJ指标是随机指标。它在设计中充分考虑了价格波动的随机振幅与中短期的波动测算，使其短期测市功能比移动均线更加准确有效。KDJ策略主要用于短期测市功能。在市场短期超买超卖的预测方面，它比相对强弱指标更敏感。

以下是KDJ策略的核心买卖信号：
*   **买入信号**：当K值在20左右向上交叉D值时，买入股票。
*   **卖出信号**：当K值在80左右向下交叉D值时，卖出股票。

![](img/d4ba5d0937d320785e54822884d276bb_1.png)

## 💡 KDJ策略实现思路

接下来，我们介绍KDJ量化交易策略的实现框架。由于KDJ策略比双均线策略稍复杂，我们将采用更完整的框架。

以下是实现步骤：
1.  **导入必要的函数库**。
2.  **设定初始化函数**，包括基准、复权方式、交易成本等。
3.  **实现开盘前运行函数**，用于每日开盘前的准备工作。
4.  **实现开盘时运行函数**，这是策略的核心，用于识别交易信号并执行交易。
5.  **实现收盘后运行函数**，用于每日收盘后的数据记录与分析。

## 💻 代码实战

现在，我们进入代码实战部分。我们将使用聚宽量化平台进行演示。

首先，导入必要的库并设置初始化函数。

```python
# 导入函数库
from jqdata import *
import numpy as np

# 初始化函数
def initialize(context):
    # 设定基准为沪深300
    set_benchmark('000300.XSHG')
    # 设定动态复权模式
    set_option('use_real_price', True)
    # 设置交易成本
    set_order_cost(OrderCost(open_tax=0, close_tax=0.001, open_commission=0.0003, close_commission=0.0003, close_today_commission=0, min_commission=5), type='stock')
```

接着，我们定义每日运行的三个时间点函数：开盘前、开盘时、收盘后。

```python
def initialize(context):
    # ... 上述初始化代码 ...
    # 开盘前运行
    run_daily(before_market_open, time='before_open', reference_security='000300.XSHG')
    # 开盘时运行
    run_daily(market_open, time='open', reference_security='000300.XSHG')
    # 收盘后运行
    run_daily(after_market_close, time='after_close', reference_security='000300.XSHG')
```

然后，我们实现开盘前的函数，这里主要进行日志输出和选定交易股票。

```python
def before_market_open(context):
    # 输出运行时间
    log.info('函数运行时间(before_market_open):' + str(context.current_dt.time()))
    # 选定交易股票，这里以平安银行为例
    g.security = '000001.XSHE'
```

接下来是实现策略核心的开盘时函数。这里会计算KDJ指标，并根据买卖信号执行交易。

```python
def market_open(context):
    log.info('函数运行时间(market_open):' + str(context.current_dt.time()))
    # 获取交易股票
    security = g.security

    # 获取KDJ指标的K值和D值 (默认参数N=9, M1=3, M2=3)
    k, d = get_kd(security, check_date=context.current_dt, N=9, M1=3, M2=3)

    # 获取当前现金
    cash = context.portfolio.available_cash

    # 买入信号：K在20左右向上交叉D
    if k < 20 and d < 20 and k > d:
        log.info('触发买入信号，K值: %.2f, D值: %.2f' % (k, d))
        # 全仓买入
        order_value(security, cash)

    # 卖出信号：K在80左右向下交叉D，并且当前持有该股票
    if k > 80 and d > 80 and k < d and context.portfolio.positions[security].closeable_amount > 0:
        log.info('触发卖出信号，K值: %.2f, D值: %.2f' % (k, d))
        # 全仓卖出
        order_target(security, 0)
```

最后，实现收盘后函数，用于记录交易详情。

![](img/d4ba5d0937d320785e54822884d276bb_3.png)

```python
def after_market_close(context):
    log.info('函数运行时间(after_market_close):' + str(context.current_dt.time()))
    # 获取当天所有成交记录
    trades = get_trades()
    for _trade in trades.values():
        log.info('成交记录: ' + str(_trade))
    log.info('一天结束')
    log.info('##############################################################')
```

## 📊 策略回测与结果分析

代码编写完成后，我们进行回测。回测时间段设为2022年8月1日至2023年2月1日。

回测结果显示：
*   **策略收益**：10.7%（年化收益约22%）
*   **基准收益（沪深300）**：0.62%
*   **超额收益**：9.69%
*   **最大回撤**：14%
*   **夏普比率**：0.7

从结果看，该KDJ策略在测试期内显著跑赢了基准指数，年化收益可观。但策略波动率和最大回撤也较高，这是短线交易策略的常见特征。

策略有两个主要的优化方向：
1.  **选股优化**：本例中只使用了平安银行一只股票。可以结合基本面选股策略，从沪深300或上证50等指数成分股中筛选股票池，可能获得更好效果。
2.  **参数优化**：买卖信号的阈值（目前是20和80）可以调整，例如尝试65和30。可以通过历史数据回测或机器学习方法寻找更优参数。

## 🎯 本章小结

本节课中我们一起学习了KDJ量化交易策略的完整实现。

首先，我们介绍了KDJ策略的核心原理，其关键买卖信号是：**K值在20左右上穿D值买入，K值在80左右下穿D值卖出**。

接着，我们讲解了策略的实现思路。由于KDJ策略相对复杂，我们采用了包含`开盘前`、`开盘时`、`收盘后`三个运行函数的完整框架。

最后，我们进行了代码实战和回测分析。实现的策略在测试期内取得了22%的年化收益，但也表现出较高的波动性。我们指出了通过**优化选股**和**调整信号阈值**两个方向可以进一步改进策略。

![](img/d4ba5d0937d320785e54822884d276bb_5.png)

希望本教程能帮助你理解并动手实现一个基础的KDJ交易策略。