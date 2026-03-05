# 从零开始量化：第16课：CtaBackEngine的实现(1) - 策略逻辑修正与无界面回测入门

![](img/67ff5575594128cf4bcb2d33b34c6f0a_1.png)

![](img/67ff5575594128cf4bcb2d33b34c6f0a_3.png)

在本节课中，我们将要学习如何修正一个简单的双均线策略中存在的逻辑问题，并初步了解如何在不依赖图形界面的情况下，通过代码进行完整的策略回测。

上一节课我们编写了一个简单的双均线策略，但在回测中发现其交易逻辑存在问题。本节中我们来看看如何修正这个问题，并学习如何通过纯代码的方式执行回测流程。

![](img/67ff5575594128cf4bcb2d33b34c6f0a_5.png)

![](img/67ff5575594128cf4bcb2d33b34c6f0a_7.png)

## 🐛 策略逻辑问题分析

在上一节课的演示策略中，我们发现了交易记录异常。策略逻辑本应是“开仓”与“平仓”交替出现，但实际回测结果却出现了连续“开仓”或连续“平仓”的情况。

以下是问题策略的核心逻辑代码片段：
```python
# 原策略中可能导致问题的反手逻辑示例
if 金叉条件:
    self.buy(bar.close_price, self.fixed_size)
elif 死叉条件:
    self.short(bar.close_price, self.fixed_size)
```
这种写法忽略了平仓与开仓之间的顺序依赖关系。在反手操作时，必须先完成当前持仓的平仓，才能进行反向开仓。

## 🔧 策略逻辑修正

为了解决上述问题，我们需要修改策略逻辑，确保反手操作是“先平后开”的。核心思路是：在满足新开仓条件时，先记录价格，然后优先处理平仓委托；待平仓成交且仓位为零后，再执行新的开仓操作。

以下是修正后的关键步骤：

首先，我们定义几个变量来记录开仓价格和平仓价格。
```python
self.buy_price = 0
self.short_price = 0
self.sell_price = 0
self.cover_price = 0
```

接着，在策略的 `on_bar` 方法中，当满足开仓条件时，我们不直接发送委托，而是先记录价格。
```python
if 金叉条件:
    self.buy_price = bar.close_price
elif 死叉条件:
    self.short_price = bar.close_price
```

![](img/67ff5575594128cf4bcb2d33b34c6f0a_9.png)

![](img/67ff5575594128cf4bcb2d33b34c6f0a_11.png)

然后，我们创建一个自定义的方法（注意不要与父类方法重名）来统一处理委托发送逻辑。
```python
def send_order(self):
    # 如果空仓且有买入价格记录，则执行买入开仓
    if self.pos == 0 and self.buy_price:
        self.buy(self.buy_price, self.fixed_size)
        self.buy_price = 0  # 重置价格记录

    # 如果空仓且有卖出开仓价格记录，则执行卖出开仓
    elif self.pos == 0 and self.short_price:
        self.short(self.short_price, self.fixed_size)
        self.short_price = 0

    # 如果持有多仓且有卖出平仓价格记录，则执行卖出平仓
    elif self.pos > 0 and self.sell_price:
        self.sell(self.sell_price, abs(self.pos), stop=True)
        self.sell_price = 0

    # 如果持有空仓且有买入平仓价格记录，则执行买入平仓
    elif self.pos < 0 and self.cover_price:
        self.cover(self.cover_price, abs(self.pos), stop=True)
        self.cover_price = 0
```

![](img/67ff5575594128cf4bcb2d33b34c6f0a_13.png)

![](img/67ff5575594128cf4bcb2d33b34c6f0a_15.png)

最后，在 `on_trade` 回调函数中，我们监听成交。如果成交是平仓且仓位变为零，则触发一次 `send_order` 方法，此时若有记录的开仓价格，就会执行新的开仓，从而实现“先平后开”。
```python
def on_trade(self, trade: TradeData):
    if trade.offset == Offset.CLOSE and self.pos == 0:
        self.send_order()
    # 保存数据等其他操作...
```

![](img/67ff5575594128cf4bcb2d33b34c6f0a_17.png)

通过以上修改，策略的交易逻辑就符合了“开平交替”的预期。

## 📊 无界面回测流程

在图形界面中回测成功后，我们进一步学习如何通过纯代码（无界面）执行回测。这有助于我们理解回测引擎的内部流程，并为后续自定义回测引擎打下基础。

整个无界面回测流程可以分为以下几个步骤：

### 第一步：数据下载
回测需要历史数据。我们首先将所需合约的历史K线数据下载并保存到数据库中。
以下是数据下载的核心代码：
```python
from vnpy.app.cta_strategy.backtesting import BacktestingEngine
from vnpy.trader.object import HistoryRequest
from vnpy.trader.constant import Exchange, Interval
import datetime

# 1. 初始化数据服务（如RQData）
feed = RqDataFeed()
feed.init()

# 2. 创建历史数据请求
req = HistoryRequest(
    symbol="J2305",
    exchange=Exchange.DCE,
    start=datetime.datetime(2022, 1, 1),
    end=datetime.datetime.now(),
    interval=Interval.MINUTE
)

# 3. 查询历史数据
bars = feed.query_bar_history(req)

# 4. 保存数据到数据库
database = BaseDatabase()
database.save_bar_data(bars)
print(f"历史数据保存成功，共{bars}条")
```

### 第二步：动态加载策略
我们需要将编写好的策略文件动态加载到程序中。
```python
import importlib

# 策略文件所在模块路径
module_name = "strategies.avg_demo.AvgDemo"
# 动态导入模块
module = importlib.import_module(module_name)
# 获取策略类
strategy_class = getattr(module, "AvgDemo")
```

### 第三步：初始化回测引擎并设置参数
创建回测引擎实例，并设置合约、周期、手续费、资金等参数。
```python
# 创建回测引擎实例
engine = BacktestingEngine()

# 添加策略，并传入策略参数（如均线周期）
engine.add_strategy(strategy_class, {})

# 设置回测参数
engine.set_parameters(
    vt_symbol="J2305.DCE",
    interval=Interval.MINUTE,
    start=datetime.datetime(2022, 1, 1),
    end=datetime.datetime.now(),
    rate=0.0,  # 手续费率
    slippage=0.0,  # 滑点
    size=100,  # 合约乘数（焦炭）
    pricetick=0.5,  # 最小价格变动
    capital=100_000,  # 初始资金
)
```

### 第四步：加载数据并运行回测
从数据库中加载K线数据，然后运行回测。
```python
# 加载历史数据
engine.load_data()
print(f"数据加载量：{len(engine.history_data)}")

# 运行回测
engine.run_backtesting()
```

### 第五步：计算并输出结果
回测结束后，计算各项绩效指标，并可以输出为表格或图表。
```python
import pandas as pd

# 计算每日结果，得到一个DataFrame
df_result = engine.calculate_result()
df_result.to_csv("backtest_result.csv")

# 计算汇总绩效指标
statistics = engine.calculate_statistics(output=True)
print(statistics)

# 绘制回测图表
engine.show_chart()
```

## 🎯 本节课总结

在本节课中，我们一起学习了两个核心内容：

1.  **策略逻辑修正**：我们分析了简单双均线策略中“反手”逻辑的错误，并通过引入价格记录变量和委托状态管理，实现了“先平仓，后开仓”的正确交易流程。关键在于利用 `on_trade` 回调函数在平仓成交后触发新的开仓。

![](img/67ff5575594128cf4bcb2d33b34c6f0a_19.png)

![](img/67ff5575594128cf4bcb2d33b34c6f0a_21.png)

2.  **无界面回测入门**：我们掌握了通过纯Python代码执行完整回测的流程，共分为五个步骤：下载数据、动态加载策略、设置回测参数、加载数据运行回测、计算并分析结果。这帮助我们理解了 `BacktestingEngine` 的基本工作流程。

![](img/67ff5575594128cf4bcb2d33b34c6f0a_23.png)

理解并掌握这个代码回测流程至关重要，它是我们下一节课自己动手实现一个简易回测引擎的基础。通过剖析这个过程，我们能更深入地理解量化回测的核心原理。