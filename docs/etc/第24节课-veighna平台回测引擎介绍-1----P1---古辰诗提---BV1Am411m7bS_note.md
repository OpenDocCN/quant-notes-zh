# 量化交易：第24课：VeighNa平台回测引擎介绍(1) 🧮

在本节课中，我们将学习VeighNa平台中CTA策略回测引擎的核心结构与逻辑。我们将深入分析其代码实现，并与我们之前手动编写的回测代码进行对比，帮助你理解一个成熟回测引擎的设计思路和优化细节。

## 课程概述 📋

本节课我们将拆解VeighNa平台中`cta_backtesting`模块的`BacktestingEngine`类。我们将重点关注其初始化、数据加载、回测运行以及每日结果计算等核心方法，理解其与我们之前编写的简易回测代码的异同，并探讨其中涉及的性能优化与设计考量。

![](img/52382bc652ac62fa7d79ecb609749d3d_1.png)

---

![](img/52382bc652ac62fa7d79ecb609749d3d_3.png)

## 引擎定位与设计考量 🤔

![](img/52382bc652ac62fa7d79ecb609749d3d_5.png)

上一节我们介绍了课程的整体目标，本节我们首先来看看为什么VeighNa将回测引擎放在CTA模块下，以及其背后的设计考量。

![](img/52382bc652ac62fa7d79ecb609749d3d_7.png)

![](img/52382bc652ac62fa7d79ecb609749d3d_9.png)

VeighNa将回测引擎置于CTA策略模块下，主要基于两个考量：

1.  **策略类型限定**：该回测引擎专为CTA策略设计。对于包含特殊操作（如FAK/FOK等询价委托模式）的高频或其他策略，无法在此引擎中进行有效回测，需要通过实盘或模拟交易进行检测。
2.  **模块导入便利**：在此位置可以方便地导入专为CTA策略定制的模块。例如，可以直接通过`.`操作导入`Base`、`Template`等类，使得代码结构更加清晰。

![](img/52382bc652ac62fa7d79ecb609749d3d_11.png)

核心的回测逻辑位于`BacktestingEngine`类中，而`CtaBacktester`类则为其添加了图形用户界面的包装，使其更易于操作。

---

![](img/52382bc652ac62fa7d79ecb609749d3d_13.png)

![](img/52382bc652ac62fa7d79ecb609749d3d_15.png)

## 核心类：BacktestingEngine 初始化 🏗️

接下来，我们深入核心部分，首先看看`BacktestingEngine`类的初始化过程。

![](img/52382bc652ac62fa7d79ecb609749d3d_17.png)

![](img/52382bc652ac62fa7d79ecb609749d3d_19.png)

`BacktestingEngine`的`__init__`方法主要进行一些属性的初始化赋值。以下是一些关键属性：

*   `capital`：初始资本金。
*   `annual_days`：预设的一年交易天数，通常为240天。
*   `mode`：回测模式，区分K线（Bar）数据和Tick数据。

与我们之前编写的代码相比，VeighNa的引擎在订单管理上更为细致。例如：

*   `active_stop_orders` 和 `stop_orders`：我们之前仅用`stop_orders`列表存储停止单，触发后直接删除。而VeighNa使用`active_stop_orders`管理活跃停止单，触发后将其移至`stop_orders`列表中进行保存，以便后续在UI界面中展示委托记录。
*   `active_limit_orders` 和 `limit_orders`：限价单的处理逻辑类似。
*   `daily_results` 和 `daily_df`：用于按日汇总权益和计算最终指标，数据类型为字典和Pandas DataFrame。

```python
# 示例：BacktestingEngine 初始化片段（概念示意）
class BacktestingEngine:
    def __init__(self):
        self.capital = 1_000_000  # 初始资金
        self.annual_days = 240    # 年交易日
        self.mode = "bar"         # 回测模式
        self.active_stop_orders = {}  # 活跃停止单
        self.stop_orders = []         # 已触发停止单
        self.daily_results = {}       # 每日结果字典
        self.daily_df = None          # 每日结果DataFrame
```

`clear_data`方法用于清空引擎中的数据字典和策略实例。这在图形界面中尤其有用，因为程序持续运行，在开始新的回测前需要手动清除旧数据，避免混淆。

---

## 数据加载的优化策略 ⚡

在初始化之后，回测需要加载历史数据。VeighNa的`load_data`方法比我们之前写的更为复杂和优化。

该方法的核心功能是从数据库下载数据并存储到`self.history_data`中。其优化主要体现在两点：

1.  **分段下载与进度显示**：为了避免一次性加载大量数据（尤其是Tick数据）时程序“卡顿”，并配合UI显示进度条，引擎将总时间段分成若干小段进行循环下载。
    *   计算总天数（`total_days`）。
    *   将总天数分为10份（`progress_days = int(total_days / 10)`）。
    *   使用`while`循环，每次下载一小段（例如25天）的数据，并更新进度显示。

2.  **使用缓存装饰器**：`load_bar`和`load_tick`方法使用了`@lru_cache`装饰器。这是一个重要的性能优化。
    *   **作用**：`@lru_cache`会缓存函数调用的结果。当使用相同的参数再次调用该函数时，会直接返回缓存中的结果，而无需重新执行函数逻辑（如从数据库查询）。
    *   **好处**：在图形界面中，当用户修改策略参数后再次点击“回测”时，如果历史数据时间段未变，引擎可以直接从缓存读取数据，极大提升了重复回测的速度。

```python
from functools import lru_cache

# 使用 lru_cache 的示例
@lru_cache(maxsize=5) # 最多缓存5个不同的调用结果
def load_historical_data(start_date, end_date):
    print(f"正在下载数据从 {start_date} 到 {end_date}...")
    # 模拟从数据库获取数据的耗时操作
    return [f"Bar_{i}" for i in range(10)]

# 第一次调用，会执行函数
data1 = load_historical_data("2023-01-01", "2023-01-10")
# 第二次用相同参数调用，直接返回缓存结果，不会打印
data2 = load_historical_data("2023-01-01", "2023-01-10")
```

![](img/52382bc652ac62fa7d79ecb609749d3d_21.png)

![](img/52382bc652ac62fa7d79ecb609749d3d_23.png)

---

## 回测运行的核心逻辑 🔄

数据加载完毕，接下来就是执行回测的核心环节——`run_backtesting`方法。

此方法的逻辑与我们之前编写的类似，但结构更清晰，并同样支持进度显示。主要步骤如下：

1.  **策略初始化**：
    *   遍历历史数据的前N根Bar（N由策略的`load_bar`天数决定）。
    *   通过计数器`day_count`判断是否过了设定的初始化天数。
    *   初始化完成后，设置`self.dt = bar.datetime`，并调用策略的`on_start`方法。

2.  **正式回测循环**：
    *   从初始化结束后的Bar开始，遍历剩余的所有历史数据。
    *   在循环中，对于每个Bar，先调用`cross_limit_order`和`cross_stop_order`方法撮合限价单和停止单。
    *   然后更新引擎的当前Bar（`self.bar`）和当前时间（`self.datetime`）。
    *   最后，根据回测模式（Bar或Tick），调用策略的`on_bar`或`on_tick`方法，驱动策略逻辑。

![](img/52382bc652ac62fa7d79ecb609749d3d_25.png)

![](img/52382bc652ac62fa7d79ecb609749d3d_27.png)

该方法同样将整个回测过程分成了若干块来执行，并更新进度条，原理与`load_data`中的分段类似。

---

## 每日结果的计算与分析 📊

回测结束后，需要计算各项绩效指标。VeighNa通过`DailyResult`类和`calculate_result`方法来实现按日统计。

以下是`DailyResult`类的核心构成：

*   **属性**：记录一个交易日内的交易次数、成交量、成交额、持仓盈亏、交易盈亏、总盈亏、净盈亏等。
*   **方法**：`add_trade`用于添加当日的交易记录；`calculate_pnl`是核心，用于计算该日的各项盈亏。

在回测过程中，`BacktestingEngine`的`update_daily_close`方法会在每根Bar结束时被调用，用于创建或更新对应日期的`DailyResult`对象，并将当日的收盘价记录进去。

`calculate_result`方法则遍历`self.daily_results`中的所有`DailyResult`对象，执行以下操作：

1.  计算每日的持仓盈亏（`holding_pnl`）：使用当日收盘价与前一日收盘价的差值，乘以当日初始持仓。**注意**：这个计算方式在日内有交易时可能存在偏差。
2.  计算每日的交易盈亏（`trading_pnl`）：遍历该日所有成交记录，根据成交方向、价格和数量精确计算。
3.  汇总计算总盈亏（`total_pnl`）和净盈亏（`net_pnl`），其中净盈亏扣除了手续费和滑点成本。
4.  最后，将所有日结果字典转换为Pandas DataFrame（`self.daily_df`），便于后续分析和可视化。

```python
# DailyResult.calculate_pnl 方法核心计算逻辑示意（非完整代码）
for trade in self.trades:
    # 计算该笔交易引起的持仓变化量
    volume = trade.volume if trade.direction == Direction.LONG else -trade.volume
    # 计算该笔交易的盈亏
    trade_pnl = volume * (self.close_price - trade.price) * trade.size
    self.trading_pnl += trade_pnl
    # 计算成交额、手续费、滑点...
```

**重要提示**：回测引擎的计算逻辑（尤其是持仓盈亏部分）为了通用性可能做出妥协，可能与实际情况或你的精确计算需求有细微差异。因此，不要完全迷信任何平台的回测结果，理解其计算逻辑并根据自身策略特点进行验证或调整至关重要。

---

![](img/52382bc652ac62fa7d79ecb609749d3d_29.png)

## 课程总结 🎯

![](img/52382bc652ac62fa7d79ecb609749d3d_31.png)

本节课我们一起深入学习了VeighNa平台CTA回测引擎的第一部分核心内容。我们分析了`BacktestingEngine`的初始化过程、数据加载的优化策略（分段下载与缓存）、回测运行的核心循环逻辑，以及每日结果的计算方式。

关键要点包括：
1.  回测引擎专为CTA策略设计，并与图形界面结合。
2.  代码在订单管理、数据加载（`@lru_cache`）等方面进行了工程化优化。
3.  回测运行流程清晰，分为策略初始化和正式循环两个阶段。
4.  绩效统计按日进行，但需注意其计算方式可能存在的通用性偏差。

![](img/52382bc652ac62fa7d79ecb609749d3d_33.png)

理解这些底层逻辑，不仅能帮助你更好地使用VeighNa平台，更能让你在需要自定义或优化回测流程时胸有成竹。在接下来的课程中，我们将继续探讨该引擎的其他部分。