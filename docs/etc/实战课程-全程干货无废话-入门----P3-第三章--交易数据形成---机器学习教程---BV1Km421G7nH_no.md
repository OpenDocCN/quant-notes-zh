# AI量化交易实战课程：P3：交易数据形成的价格

![](img/cffc46c367983651a2fa6f4ee05480ef_1.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_3.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_5.png)

## 概述
在本节课中，我们将学习如何利用交易数据构建价格序列，并以此为基础搭建一个简单的回测系统。我们将从最直观的Excel工具开始，逐步过渡到使用Python实现向量化回测，并最终探讨事件驱动型回测引擎的设计理念。通过本节课，你将理解从原始数据到净值曲线的完整计算流程。

## 第一部分：使用Excel进行简单回测

上一节我们介绍了如何获取和处理原始交易数据。本节中，我们来看看如何利用这些数据，在Excel中实现一个简单的策略回测。

### 数据准备与移动平均线
我们使用第一节课作业中合成的分钟线（Bar）数据。数据包含`datetime`和`close`两列。需要注意的是，`datetime`为`21:00`的Bar代表从`21:00`到`21:01`（不含）这一分钟内的所有成交信息。交易日通常从前一晚的夜盘开始计算。

首先，我们引入一个核心概念：**移动平均线**。移动平均线（Moving Average， 简称MA）是取一定周期内价格的平均值，并随时间窗口滑动计算。例如，`MA5`表示取当前Bar及前4根Bar（共5根）的收盘价平均值。

![](img/cffc46c367983651a2fa6f4ee05480ef_7.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_9.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_11.png)

在Excel中，我们可以使用公式（如`AVERAGE`函数）来填充计算`MA5`和`MA20`。

![](img/cffc46c367983651a2fa6f4ee05480ef_13.png)

**公式示例（假设收盘价在C列，从第2行开始）：**
```
MA5: =AVERAGE(C2:C6)  // 计算第6行（即第5个有效数据点）的MA5
```

### 构建交易策略
我们构建一个基于双均线的简单策略：
*   当 `MA5 >= MA20` 时，做多一手。
*   当 `MA5 < MA20` 时，做空一手。

在Excel中，我们可以通过比较`MA5`和`MA20`两列来生成信号，再将信号（如1代表多，-1代表空）转化为仓位。

### 计算损益与净值曲线
计算损益是回测的核心。我们需要区分两种损益：
1.  **持仓损益**：由于价格变动，当前持仓产生的浮动盈亏。
2.  **交易损益**：由于仓位变化（开仓、平仓）产生的已实现盈亏。

![](img/cffc46c367983651a2fa6f4ee05480ef_15.png)

**持仓损益公式：**
`Position PnL = 当前持仓手数 * (当前Bar收盘价 - 上一个Bar收盘价) * 合约乘数`

![](img/cffc46c367983651a2fa6f4ee05480ef_17.png)

**交易损益公式：**
`Trade PnL = (当前持仓手数 - 上一个Bar持仓手数) * (-上一个Bar收盘价) * 合约乘数`
*注意：公式中的负号体现了价格变动与仓位变动对损益影响的相反关系。*

**总损益公式：**
`Total PnL = Position PnL + Trade PnL`

以下是计算步骤：
1.  计算每一分钟的`Position PnL`和`Trade PnL`。
2.  将每一分钟的`Total PnL`累加，得到累计损益`Cumulative PnL`。
3.  设定初始资金（例如10，000元），用初始资金加上`Cumulative PnL`，得到资金曲线`Capital`。
4.  计算净值`Net Asset Value`：`NAV = Capital / 初始资金`。
5.  也可计算每分钟收益率：`Return = (当前Capital - 上一个Bar Capital) / 上一个Bar Capital`，然后通过`NAV = 前一个NAV * (1 + 当前Return)`的方式复利计算净值。两种方法结果应一致。

通过以上步骤，我们就在Excel中完成了一次完整的策略回测，并得到了净值曲线图。

## 第二部分：使用Python实现向量化回测

![](img/cffc46c367983651a2fa6f4ee05480ef_19.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_21.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_22.png)

虽然Excel直观，但处理大量数据或复杂策略时效率低下。本节中，我们来看看如何用Python实现一个高效、向量化的回测工具。

![](img/cffc46c367983651a2fa6f4ee05480ef_24.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_26.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_28.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_30.png)

### 向量化计算的优势
向量化操作利用`Pandas`和`NumPy`库，对整个数据序列（DataFrame的列）进行一次性计算，避免了低效的循环，速度极快。

![](img/cffc46c367983651a2fa6f4ee05480ef_32.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_33.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_34.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_35.png)

我们的目标是复现Excel中的回测逻辑，但全部用Python代码实现。核心步骤包括：
1.  计算`MA5`和`MA20`。
2.  生成交易信号和仓位。
3.  计算`Position PnL`、`Trade PnL`和`Total PnL`。
4.  计算资金曲线和净值。

![](img/cffc46c367983651a2fa6f4ee05480ef_37.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_39.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_41.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_43.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_45.png)

**代码结构示意：**
```python
import pandas as pd
# 假设df包含‘close’列
df[‘MA5’] = df[‘close’].rolling(5).mean()
df[‘MA20’] = df[‘close’].rolling(20).mean()
df[‘signal’] = (df[‘MA5’] >= df[‘MA20’]).astype(int) * 2 - 1  # 生成1（多）和-1（空）的信号
df[‘position’] = df[‘signal’].shift(1)  # 仓位在下一根Bar生效
# 计算PnL
df[‘price_diff’] = df[‘close’].diff()
df[‘position_pnl’] = df[‘position’] * df[‘price_diff’] * contract_multiplier
df[‘trade_pnl’] = df[‘position’].diff() * (-df[‘close’].shift(1)) * contract_multiplier
df[‘total_pnl’] = df[‘position_pnl’] + df[‘trade_pnl’]
df[‘cumulative_pnl’] = df[‘total_pnl’].cumsum()
initial_capital = 10000
df[‘capital’] = initial_capital + df[‘cumulative_pnl’]
df[‘nav’] = df[‘capital’] / initial_capital
```

### 封装成回测类
为了便于测试不同策略和参数，我们将上述逻辑封装成一个`VectorizedBacktester`类。该类定义标准接口，不同的策略只需继承该类并实现`generate_signal`方法即可。

**类的核心结构：**
```python
class VectorizedBacktester:
    def __init__(self, data_handler):
        self.df = data_handler.get_data().copy()
        self.initial_capital = 10000
        self.contract_multiplier = 10  # 例如螺纹钢

    def generate_signal(self):
        """由子策略实现，负责填充self.df[‘signal‘]"""
        raise NotImplementedError

    def calculate_performance(self):
        """根据signal计算仓位、损益和净值"""
        # … 实现上述向量化计算逻辑
        return performance_metrics
```
通过这种设计，策略研究和回测执行实现了分离，提高了代码的复用性和可测试性。

## 第三部分：事件驱动型回测引擎设计

向量化回测虽然高效，但难以模拟真实交易中的复杂场景，如止盈止损、挂单成交、多合约价差等。本节中，我们来看看更贴近实战的事件驱动型回测引擎是如何设计的。

### 为什么需要事件驱动？
真实交易是随着时间流逝，由一系列事件（行情到达、订单发出、订单成交等）推动的。为了更精确地模拟，我们需要一个能够按事件发生顺序处理这些场景的引擎。

![](img/cffc46c367983651a2fa6f4ee05480ef_47.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_49.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_51.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_53.png)

### 核心概念：事件队列
我们引入一个**先进先出队列**来管理所有事件。整个回测过程可以看作不断从队列中取出事件，并根据事件类型分发给相应处理模块的过程。

![](img/cffc46c367983651a2fa6f4ee05480ef_55.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_57.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_59.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_61.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_63.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_65.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_67.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_69.png)

主要涉及四类核心组件：
1.  **市场数据引擎**：负责按时间顺序发布历史行情事件到队列。
2.  **策略类**：从队列读取行情事件，经过计算后，产生交易信号事件并放回队列。
3.  **投资组合类**：从队列读取交易信号事件，结合当前持仓、资金等情况，产生订单事件并放回队列。它也负责处理成交回报事件，更新持仓和资金。
4.  **执行引擎**：从队列读取订单事件，根据当前市场行情（盘口）判断能否成交，若能成交则产生成交回报事件并放回队列。

![](img/cffc46c367983651a2fa6f4ee05480ef_71.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_73.png)

### 工作流程
以下是事件驱动的核心循环流程：

![](img/cffc46c367983651a2fa6f4ee05480ef_75.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_77.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_79.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_81.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_83.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_85.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_87.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_89.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_91.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_93.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_95.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_97.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_99.png)

1.  **初始化**：市场数据引擎将第一个行情事件放入队列。
2.  **循环处理**：
    *   从事件队列中取出最靠前的事件。
    *   **如果事件是行情**：交给策略类处理。策略类计算后，可能产生新的信号事件放入队列。
    *   **如果事件是信号**：交给投资组合类处理。投资组合类决定下单，产生新的订单事件放入队列。
    *   **如果事件是订单**：交给执行引擎处理。执行引擎尝试撮合，若成功则产生成交事件放入队列。
    *   **如果事件是成交**：交给投资组合类处理。投资组合类更新持仓和资金。根据策略，可能触发新的操作（如止盈止损），产生新的事件放入队列。
3.  **循环继续**：只要队列不为空，就重复步骤2。当所有历史数据消费完毕且队列为空时，回测结束。

![](img/cffc46c367983651a2fa6f4ee05480ef_101.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_103.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_105.png)

**流程示意代码：**
```python
while True:
    if event_queue.is_empty():
        if not market_data_engine.has_more_data():
            break  # 回测结束
        else:
            market_data_engine.publish_next_event(event_queue)
            continue

    event = event_queue.get()
    if event.type == ‘MARKET_DATA‘:
        strategy.process_market_data(event, event_queue)
    elif event.type == ‘SIGNAL‘:
        portfolio.process_signal(event, event_queue)
    elif event.type == ‘ORDER‘:
        execution.process_order(event, event_queue)
    elif event.type == ‘FILL‘:
        portfolio.process_fill(event, event_queue)
```
这种架构高度模块化，每个组件职责清晰，易于扩展和维护，能够支持非常复杂的交易逻辑模拟。

![](img/cffc46c367983651a2fa6f4ee05480ef_107.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_109.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_111.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_113.png)

![](img/cffc46c367983651a2fa6f4ee05480ef_115.png)

## 总结
本节课中我们一起学习了回测系统的构建。我们从最简单的Excel工具入手，理解了损益计算和净值曲线生成的原理。接着，我们使用Python实现了向量化回测，提升了计算效率并进行了初步的代码封装。最后，我们探讨了事件驱动型回测引擎的设计理念，了解了如何通过事件队列来模拟真实、复杂的交易场景。掌握这些知识，是构建可靠、可扩展的量化研究平台的基础。