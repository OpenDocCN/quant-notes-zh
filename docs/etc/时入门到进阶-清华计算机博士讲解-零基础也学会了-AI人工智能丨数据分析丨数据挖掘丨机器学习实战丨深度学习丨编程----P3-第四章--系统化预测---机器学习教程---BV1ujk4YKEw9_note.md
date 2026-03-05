# AI量化交易实战教程：第四章：系统化预测 📈

![](img/d9039bbe1192e4789714e90099bfd010_0.png)

![](img/d9039bbe1192e4789714e90099bfd010_1.png)

![](img/d9039bbe1192e4789714e90099bfd010_2.png)

![](img/d9039bbe1192e4789714e90099bfd010_3.png)

![](img/d9039bbe1192e4789714e90099bfd010_4.png)

![](img/d9039bbe1192e4789714e90099bfd010_5.png)

![](img/d9039bbe1192e4789714e90099bfd010_6.png)

![](img/d9039bbe1192e4789714e90099bfd010_7.png)

![](img/d9039bbe1192e4789714e90099bfd010_8.png)

![](img/d9039bbe1192e4789714e90099bfd010_9.png)

![](img/d9039bbe1192e4789714e90099bfd010_10.png)

![](img/d9039bbe1192e4789714e90099bfd010_11.png)

![](img/d9039bbe1192e4789714e90099bfd010_13.png)

![](img/d9039bbe1192e4789714e90099bfd010_15.png)

在本节课中，我们将学习如何构建一个事件驱动的回测系统，并探讨如何系统化地挖掘和构建用于预测市场走势的因子。我们将从回测系统的核心组件讲起，然后过渡到因子研究的基本方法。

## 回顾：事件驱动回测系统 🔄

上一节我们介绍了量化交易的基本概念，本节中我们来看看事件驱动回测系统的核心架构。整个系统基于一个双层循环结构，核心是处理一系列按时间顺序排列的事件。

整个回测流程的核心代码如下所示：

```python
while self.continue_backtest:
    # 第一层循环：检查数据引擎是否还有数据
    if self.market_data_engine.continue_backtest:
        bar = self.market_data_engine.publish_market_data()
        self.event_q.put(bar)
    else:
        break

    # 第二层循环：处理事件队列中的所有事件
    while True:
        try:
            event = self.event_q.get(block=False)
        except queue.Empty:
            break

        if event is None:
            continue

        if event.type == ‘BAR’:
            self.strategy.on_bar(event)
        elif event.type == ‘SIGNAL’:
            self.portfolio.on_signal(event)
        elif event.type == ‘ORDER’:
            self.execution.on_order(event)
        elif event.type == ‘FILL’:
            self.portfolio.on_fill(event)
```

![](img/d9039bbe1192e4789714e90099bfd010_17.png)

![](img/d9039bbe1192e4789714e90099bfd010_19.png)

![](img/d9039bbe1192e4789714e90099bfd010_21.png)

以下是系统中涉及的几个核心类及其职责：

![](img/d9039bbe1192e4789714e90099bfd010_23.png)

![](img/d9039bbe1192e4789714e90099bfd010_25.png)

![](img/d9039bbe1192e4789714e90099bfd010_27.png)

![](img/d9039bbe1192e4789714e90099bfd010_29.png)

![](img/d9039bbe1192e4789714e90099bfd010_31.png)

![](img/d9039bbe1192e4789714e90099bfd010_33.png)

![](img/d9039bbe1192e4789714e90099bfd010_35.png)

*   **DataHandler**：负责加载市场数据，并按时间顺序发布`Bar`事件。
*   **Strategy**：接收`Bar`事件，根据策略逻辑生成交易`Signal`事件。
*   **Portfolio**：接收`Signal`事件，结合当前持仓和资金管理规则，生成`Order`事件。
*   **Execution**：接收`Order`事件，模拟交易所撮合，根据设定的成交规则生成`Fill`事件。

![](img/d9039bbe1192e4789714e90099bfd010_37.png)

![](img/d9039bbe1192e4789714e90099bfd010_39.png)

当所有数据处理完毕后，系统需要执行收尾工作，包括撤销所有未成交订单和平掉所有未平仓头寸，以确保回测结束时账户状态清晰。

![](img/d9039bbe1192e4789714e90099bfd010_41.png)

![](img/d9039bbe1192e4789714e90099bfd010_43.png)

## 核心组件详解 ⚙️

上一节我们概述了系统的整体流程，本节中我们来看看每个核心类的具体实现细节。

![](img/d9039bbe1192e4789714e90099bfd010_45.png)

![](img/d9039bbe1192e4789714e90099bfd010_47.png)

![](img/d9039bbe1192e4789714e90099bfd010_49.png)

### DataHandler：数据加载与发布

![](img/d9039bbe1192e4789714e90099bfd010_51.png)

![](img/d9039bbe1192e4789714e90099bfd010_53.png)

![](img/d9039bbe1192e4789714e90099bfd010_55.png)

![](img/d9039bbe1192e4789714e90099bfd010_57.png)

![](img/d9039bbe1192e4789714e90099bfd010_59.png)

`DataHandler` 是系统的起点。它的主要职责是从数据源（如CSV文件或数据库）加载历史数据，并按时间顺序将其包装成`Bar`事件发布到事件队列中。

![](img/d9039bbe1192e4789714e90099bfd010_61.png)

![](img/d9039bbe1192e4789714e90099bfd010_63.png)

![](img/d9039bbe1192e4789714e90099bfd010_65.png)

![](img/d9039bbe1192e4789714e90099bfd010_67.png)

需要注意的要点包括：
*   对于多品种、长时间的回测，建议将数据存入数据库（如MongoDB）以提高效率和可管理性。
*   使用生成器（`yield`）逐条返回数据，避免一次性将所有数据加载到内存。
*   使用双端队列（`deque`）缓存最近的N根`Bar`，方便策略计算指标（如移动平均线）。
*   当主力合约切换时，需要清空缓存数据。

![](img/d9039bbe1192e4789714e90099bfd010_69.png)

![](img/d9039bbe1192e4789714e90099bfd010_71.png)

![](img/d9039bbe1192e4789714e90099bfd010_73.png)

![](img/d9039bbe1192e4789714e90099bfd010_75.png)

一个简单的`Bar`事件类结构如下：

![](img/d9039bbe1192e4789714e90099bfd010_77.png)

![](img/d9039bbe1192e4789714e90099bfd010_79.png)

```python
class BarEvent(Event):
    __slots__ = [‘symbol‘, ‘datetime‘, ‘open‘, ‘high‘, ‘low‘, ‘close‘, ‘volume‘]
    def __init__(self, symbol, datetime, open, high, low, close, volume):
        self.type = ‘BAR‘
        self.symbol = symbol
        self.datetime = datetime
        self.open = open
        self.high = high
        self.low = low
        self.close = close
        self.volume = volume
```

![](img/d9039bbe1192e4789714e90099bfd010_81.png)

![](img/d9039bbe1192e4789714e90099bfd010_83.png)

![](img/d9039bbe1192e4789714e90099bfd010_85.png)

![](img/d9039bbe1192e4789714e90099bfd010_87.png)

### Strategy：策略逻辑

![](img/d9039bbe1192e4789714e90099bfd010_88.png)

![](img/d9039bbe1192e4789714e90099bfd010_89.png)

![](img/d9039bbe1192e4789714e90099bfd010_90.png)

![](img/d9039bbe1192e4789714e90099bfd010_91.png)

![](img/d9039bbe1192e4789714e90099bfd010_93.png)

![](img/d9039bbe1192e4789714e90099bfd010_95.png)

![](img/d9039bbe1192e4789714e90099bfd010_97.png)

![](img/d9039bbe1192e4789714e90099bfd010_98.png)

`Strategy` 类是所有交易策略的基类。每个具体策略（如双均线策略）都必须实现 `on_bar` 方法。该方法接收最新的`Bar`事件，计算交易信号，并生成`Signal`事件。

![](img/d9039bbe1192e4789714e90099bfd010_100.png)

![](img/d9039bbe1192e4789714e90099bfd010_102.png)

![](img/d9039bbe1192e4789714e90099bfd010_104.png)

![](img/d9039bbe1192e4789714e90099bfd010_106.png)

![](img/d9039bbe1192e4789714e90099bfd010_108.png)

![](img/d9039bbe1192e4789714e90099bfd010_109.png)

以下是`Signal`事件的一个示例：

![](img/d9039bbe1192e4789714e90099bfd010_111.png)

```python
class SignalEvent(Event):
    def __init__(self, symbol, datetime, signal_type, strength=1.0):
        self.type = ‘SIGNAL‘
        self.symbol = symbol
        self.datetime = datetime
        self.signal_type = signal_type  # ‘LONG‘, ‘SHORT‘, ‘EXIT‘
        self.strength = strength  # 信号强度，可用于仓位管理
```

![](img/d9039bbe1192e4789714e90099bfd010_113.png)

![](img/d9039bbe1192e4789714e90099bfd010_115.png)

![](img/d9039bbe1192e4789714e90099bfd010_117.png)

### Execution：订单撮合引擎

![](img/d9039bbe1192e4789714e90099bfd010_119.png)

![](img/d9039bbe1192e4789714e90099bfd010_121.png)

![](img/d9039bbe1192e4789714e90099bfd010_123.png)

![](img/d9039bbe1192e4789714e90099bfd010_125.png)

`Execution` 类模拟交易所的撮合功能。它接收`Portfolio`发出的`Order`事件，并根据当前及历史的`Bar`数据判断订单是否能成交。

![](img/d9039bbe1192e4789714e90099bfd010_127.png)

成交逻辑是回测中最精细的部分之一，直接影响结果的真实性。一种严格的限价单成交判断逻辑（以买单为例）如下：

![](img/d9039bbe1192e4789714e90099bfd010_129.png)

![](img/d9039bbe1192e4789714e90099bfd010_131.png)

![](img/d9039bbe1192e4789714e90099bfd010_133.png)

![](img/d9039bbe1192e4789714e90099bfd010_135.png)

![](img/d9039bbe1192e4789714e90099bfd010_137.png)

![](img/d9039bbe1192e4789714e90099bfd010_139.png)

```python
# 假设 last_bar 和 this_bar 是最近的两根K线
# order_price 是订单的限价
if (order_price <= last_bar.close_ask and
    order_price >= this_bar.low_ask and
    this_bar.low_ask > 0):
    # 订单可以成交
    fill_price = order_price
```

![](img/d9039bbe1192e4789714e90099bfd010_141.png)

![](img/d9039bbe1192e4789714e90099bfd010_143.png)

这个条件要求订单价格同时满足：
1.  不高于上一根`Bar`的`Ask`收盘价。
2.  不低于当前`Bar`的最低的`Ask`价。
3.  当前`Bar`的最低的`Ask`价大于0（处理涨跌停情况）。

![](img/d9039bbe1192e4789714e90099bfd010_145.png)

![](img/d9039bbe1192e4789714e90099bfd010_147.png)

![](img/d9039bbe1192e4789714e90099bfd010_149.png)

这样设计是为了模拟一个“价格下穿”的过程，确保成交价格相对理想，避免过于乐观的假设。开发者可以根据策略类型调整成交逻辑。

![](img/d9039bbe1192e4789714e90099bfd010_151.png)

![](img/d9039bbe1192e4789714e90099bfd010_153.png)

![](img/d9039bbe1192e4789714e90099bfd010_155.png)

![](img/d9039bbe1192e4789714e90099bfd010_157.png)

![](img/d9039bbe1192e4789714e90099bfd010_159.png)

![](img/d9039bbe1192e4789714e90099bfd010_161.png)

### Portfolio：资金管理与订单生成

![](img/d9039bbe1192e4789714e90099bfd010_163.png)

![](img/d9039bbe1192e4789714e90099bfd010_165.png)

![](img/d9039bbe1192e4789714e90099bfd010_167.png)

![](img/d9039bbe1192e4789714e90099bfd010_169.png)

![](img/d9039bbe1192e4789714e90099bfd010_171.png)

![](img/d9039bbe1192e4789714e90099bfd010_173.png)

![](img/d9039bbe1192e4789714e90099bfd010_175.png)

`Portfolio` 类是连接策略信号和实际交易的桥梁。它主要完成两件事：
1.  **`on_signal`**：根据收到的`Signal`事件、当前持仓、资金情况，决定下单的手数和价格，生成`Order`事件。
2.  **`on_fill`**：根据成交回报（`Fill`事件）更新持仓、记录交易记录，并管理止盈止损单。

![](img/d9039bbe1192e4789714e90099bfd010_177.png)

![](img/d9039bbe1192e4789714e90099bfd010_179.png)

![](img/d9039bbe1192e4789714e90099bfd010_181.png)

![](img/d9039bbe1192e4789714e90099bfd010_183.png)

![](img/d9039bbe1192e4789714e90099bfd010_185.png)

![](img/d9039bbe1192e4789714e90099bfd010_187.png)

![](img/d9039bbe1192e4789714e90099bfd010_189.png)

`Order`事件的结构如下：

![](img/d9039bbe1192e4789714e90099bfd010_191.png)

![](img/d9039bbe1192e4789714e90099bfd010_193.png)

![](img/d9039bbe1192e4789714e90099bfd010_195.png)

![](img/d9039bbe1192e4789714e90099bfd010_197.png)

![](img/d9039bbe1192e4789714e90099bfd010_199.png)

```python
class OrderEvent(Event):
    def __init__(self, symbol, order_type, quantity, direction, price=None):
        self.type = ‘ORDER‘
        self.symbol = symbol
        self.order_type = order_type  # ‘MARKET‘, ‘LIMIT‘
        self.quantity = quantity
        self.direction = direction    # ‘BUY‘, ‘SELL‘
        self.price = price            # 对于限价单有效
```

![](img/d9039bbe1192e4789714e90099bfd010_201.png)

![](img/d9039bbe1192e4789714e90099bfd010_203.png)

![](img/d9039bbe1192e4789714e90099bfd010_205.png)

对于初学者，建议先实现基本的开平仓功能，暂不引入复杂的止盈止损逻辑，以降低系统复杂度。

![](img/d9039bbe1192e4789714e90099bfd010_207.png)

## 系统化预测：因子研究 🔍

![](img/d9039bbe1192e4789714e90099bfd010_209.png)

在搭建好回测系统后，我们就可以系统地研究和测试交易策略。而策略的核心往往在于**因子**——那些被认为能够预测未来价格走势的指标或特征。

![](img/d9039bbe1192e4789714e90099bfd010_211.png)

![](img/d9039bbe1192e4789714e90099bfd010_213.png)

### 什么是因子？

因子可以是任何从历史数据中计算出来的值，例如：
*   简单移动平均线（SMA）
*   成交量
*   过去N根K线的平均收益率
*   价格波动率
*   等等

![](img/d9039bbe1192e4789714e90099bfd010_215.png)

![](img/d9039bbe1192e4789714e90099bfd010_217.png)

![](img/d9039bbe1192e4789714e90099bfd010_219.png)

![](img/d9039bbe1192e4789714e90099bfd010_221.png)

![](img/d9039bbe1192e4789714e90099bfd010_222.png)

![](img/d9039bbe1192e4789714e90099bfd010_223.png)

![](img/d9039bbe1192e4789714e90099bfd010_224.png)

![](img/d9039bbe1192e4789714e90099bfd010_226.png)

![](img/d9039bbe1192e4789714e90099bfd010_227.png)

我们的目标是找到那些与**未来一段时间收益率**存在稳定相关性的因子。在金融数据中，这种相关性通常非常微弱（相关系数可能只有0.01-0.1），信噪比极低，因此挖掘有效因子是一项挑战。

### 因子研究流程

![](img/d9039bbe1192e4789714e90099bfd010_229.png)

![](img/d9039bbe1192e4789714e90099bfd010_231.png)

以下是进行因子研究的基本步骤：

1.  **数据准备**：加载并清洗历史数据（如分钟级K线）。**必须注意将不同合约的数据分开处理**，避免合约换月导致的数据断层。
2.  **划分数据集**：将数据按时间分为训练集、验证集和测试集（例如70%，15%，15%）。**严禁打乱数据顺序**，必须保持时间序列结构。
3.  **因子计算**：在训练集上，为每一个时间点计算候选因子的值。
4.  **相关性分析**：计算每个因子值与未来收益率（例如下一根K线的收益率）的相关系数。可以逐个合约计算，然后观察因子在不同合约上的平均表现。
5.  **回测验证**：将初步筛选出的因子，以简单的规则（如因子值大于阈值时做多，小于阈值时做空）放入回测系统中进行验证，观察其历史表现。

### 因子来源与构建

对于初学者，可以从以下途径寻找因子灵感：

*   **技术指标库**：如`TA-Lib`库中包含上百种经典技术指标，这些都是很好的起点。
*   **学术论文与研报**：例如World Quant发布的《101 Formulaic Alphas》论文，提供了101个量化阿尔法因子公式。国内券商如国泰君安也发布过多因子选股报告。
*   **市场观察**：通过观察盘口、成交量、价格形态的变化，自己总结规律并尝试用公式描述。

构建因子时，思维要灵活。例如，不仅可以使用价格数据，还可以结合成交量（如量价结合）、跨品种数据（如螺纹钢与铁矿的价格差）、甚至更高频的tick数据（如挂单量变化）来创造新的因子。

### 注意事项

*   **避免未来函数**：确保在时间点t计算因子时，只使用了t及之前的信息。
*   **因子衰减**：有效的因子可能会随着市场参与者的学习而失效（阿尔法衰减为风险因子），需要持续研究和更新因子库。
*   **过拟合风险**：在大量因子中反复测试，很容易找到在历史数据上巧合有效的因子。必须进行严格的样本外测试。

## 总结 📝

本节课中我们一起学习了事件驱动回测系统的完整构建方法，并介绍了系统化预测的核心——因子研究。

我们首先深入剖析了回测系统的四个核心组件：`DataHandler`、`Strategy`、`Execution`和`Portfolio`，理解了它们如何通过事件队列协同工作，模拟真实的交易流程。特别强调了成交逻辑的严谨性和避免未来函数的重要性。

接着，我们过渡到如何系统化地寻找交易信号。因子研究是量化策略开发的基石，其本质是从历史数据中挖掘与未来收益相关的特征。我们介绍了因子的概念、基本研究流程以及因子的来源。重要的是要认识到，在噪声巨大的金融市场中，发现稳定有效的因子需要大量的数据、严谨的方法和持续的探索。

![](img/d9039bbe1192e4789714e90099bfd010_233.png)

建议大家在后续的学习中，亲手实现一个简单的回测框架，并尝试从TA-Lib或经典论文中实现几个因子，完成从计算、分析到回测验证的全流程，这将极大地加深对量化系统工作的理解。