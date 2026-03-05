# 量化交易：P13：第四章第1节：系统化预测 📈

![](img/b12499ea93b4ec0e5e3a6311fe89f936_0.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_1.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_2.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_3.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_4.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_5.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_6.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_7.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_8.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_9.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_10.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_11.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_13.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_15.png)

在本节课中，我们将学习如何构建一个事件驱动的回测系统，并探讨如何系统化地挖掘和构建用于预测市场走势的因子。我们将从回顾回测框架的核心组件开始，然后深入讲解数据处理器、策略、投资组合和执行引擎的实现细节，最后介绍因子研究的基本方法。

## 课程回顾与作业点评 🔍

上一节我们介绍了事件驱动回测的基本框架。本节中，我们先来回顾一下上周的作业情况。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_17.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_19.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_21.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_23.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_25.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_27.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_29.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_31.png)

以下是作业中需要注意的几个要点：
*   **交易盈亏与持仓盈亏**：`trade_pl` 和 `position_pl` 的区别在于，`trade_pl` 反映的是以实际成交价计算的单笔交易盈亏，而 `position_pl` 反映的是以当前市场价格计算的浮动盈亏。当挂单价格与分钟Bar收盘价不同时，这种区分就很重要。
*   **数据偏移（Shift）**：如果策略是基于当前Bar的信号，在下一个Bar开盘时成交，那么在计算仓位变化时需要进行 `shift` 操作，以避免使用未来数据。
*   **数据管理**：建议将数据存入数据库（如SQLite或MongoDB），便于管理多品种、长时间序列的数据，并方便后续更新和研究。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_33.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_35.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_37.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_39.png)

## 事件驱动回测框架详解 ⚙️

![](img/b12499ea93b4ec0e5e3a6311fe89f936_41.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_43.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_45.png)

现在，让我们深入理解回测框架中各个核心类的职责和工作流程。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_47.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_49.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_51.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_53.png)

整个回测系统由一个双重循环驱动。外层循环检查数据引擎是否还有数据；内层循环从一个事件队列中不断取出并处理事件，直到队列为空。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_55.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_57.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_59.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_61.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_63.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_65.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_67.png)

以下是系统中主要的四个事件及其处理者：
*   **Bar事件**：由数据引擎产生，由策略类处理。
*   **Signal事件**：由策略类产生，由投资组合类处理。
*   **Order事件**：由投资组合类产生，由执行引擎处理。
*   **Fill事件**：由执行引擎产生，由投资组合类处理。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_69.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_71.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_73.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_75.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_77.png)

核心的回测驱动函数结构如下：
```python
def run_backtest(self):
    while self.data_handler.continue_backtest:
        # 获取新Bar数据并放入事件队列
        bar_event = self.data_handler.get_next_bar()
        self.events_queue.put(bar_event)
        
        # 内层循环：处理事件队列中的所有事件
        while True:
            try:
                event = self.events_queue.get(block=False)
            except Queue.Empty:
                break
            else:
                # 根据事件类型分发给对应的处理者
                if event.type == ‘BAR’:
                    self.strategy.on_bar(event)
                elif event.type == ‘SIGNAL’:
                    self.portfolio.on_signal(event)
                elif event.type == ‘ORDER’:
                    self.execution.on_order(event)
                elif event.type == ‘FILL’:
                    self.portfolio.on_fill(event)
        # 回测结束时，强制平掉所有未平仓头寸
        self.execution.force_close_open_trades()
```

![](img/b12499ea93b4ec0e5e3a6311fe89f936_79.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_81.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_83.png)

### 1. 数据处理器（Data Handler）

![](img/b12499ea93b4ec0e5e3a6311fe89f936_85.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_87.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_88.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_89.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_90.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_91.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_92.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_94.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_96.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_97.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_99.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_101.png)

数据处理器负责加载市场数据，并将其转换为Bar事件推送到事件队列。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_103.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_105.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_107.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_108.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_110.png)

需要注意的细节包括：
*   **数据存储**：建议按品种、频率（如分钟线）组织数据文件或数据库。
*   **内存优化**：使用生成器逐条读取数据，避免一次性加载全部数据到内存。
*   **历史数据缓存**：使用一个双端队列缓存最近的Bar（例如最近2400个），方便策略计算指标（如移动平均线）。当主力合约切换时，需要清空此缓存。
*   **Bar对象**：使用 `__slots__` 属性来优化大量Bar对象的内存占用。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_112.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_114.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_116.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_118.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_120.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_122.png)

### 2. 策略类（Strategy）

![](img/b12499ea93b4ec0e5e3a6311fe89f936_124.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_126.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_128.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_130.png)

策略类接收Bar事件，根据预设逻辑生成交易信号。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_132.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_134.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_136.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_138.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_140.png)

一个简单的双均线策略示例：
```python
def on_bar(self, bar_event):
    # 更新价格序列
    self.symbol_data[bar_event.symbol].append(bar_event.close_price)
    
    # 计算均线
    if len(self.symbol_data[bar_event.symbol]) >= self.long_window:
        short_ma = np.mean(self.symbol_data[bar_event.symbol][-self.short_window:])
        long_ma = np.mean(self.symbol_data[bar_event.symbol][-self.long_window:])
        
        # 生成信号
        if short_ma > long_ma and not self.current_signal == 1:
            signal = SignalEvent(bar_event.symbol, ‘LONG’)
            self.events_queue.put(signal)
            self.current_signal = 1
        elif short_ma < long_ma and not self.current_signal == -1:
            # ... 生成空头信号
```

![](img/b12499ea93b4ec0e5e3a6311fe89f936_142.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_144.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_146.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_148.png)

### 3. 执行引擎（Execution Engine）

![](img/b12499ea93b4ec0e5e3a6311fe89f936_150.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_152.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_154.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_156.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_158.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_160.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_162.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_164.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_166.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_168.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_170.png)

执行引擎负责撮合订单。它接收Order事件，并结合最新的市场行情判断是否能成交。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_172.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_174.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_176.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_178.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_180.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_182.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_184.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_186.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_188.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_190.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_192.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_194.png)

撮合逻辑是回测中最关键且复杂的部分之一。一个相对严格的买单撮合条件示例如下：
```
成交条件： (订单价格 <= 上一Bar的Ask收盘价) 且 (订单价格 >= 当前Bar的最低Ask价) 且 (当前Bar最低Ask价 > 0)
```
这个条件模拟了市场“下穿”订单价格时成交的情景，比简单的“订单价格优于对手价”更严格，旨在避免过于乐观的回测结果。实盘中可能因速度劣势无法成交。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_196.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_198.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_200.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_202.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_204.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_206.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_208.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_210.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_212.png)

**重要建议**：初学者可以先实现最简单的撮合逻辑（例如，以下一Bar的开盘价或收盘价成交），确保整个事件驱动回测流程能跑通，并与向量化回测结果一致。之后再逐步实现更复杂的撮合逻辑。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_214.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_216.png)

### 4. 投资组合类（Portfolio）

![](img/b12499ea93b4ec0e5e3a6311fe89f936_218.png)

投资组合类是连接策略和执行的枢纽，主要完成两件事：
1.  **管理信号**：接收Signal事件，结合当前持仓、风控规则等，决定下单的手数和价格，生成Order事件。
2.  **管理成交**：接收Fill事件，更新持仓记录，计算浮动盈亏和累计盈亏。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_220.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_222.png)

关于止盈止损：
*   **基础止盈止损**：在开仓时，可以同时挂出止盈单和止损单。当其中一个成交后，需取消另一个订单。
*   **移动止损**：其核心是根据市场价格的变化，动态更新止损单的价格。例如，价格创出新高后，将止损价上调至新高回撤一定点数的位置，以保护利润。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_224.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_226.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_228.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_230.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_232.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_234.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_235.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_237.png)

![](img/b12499ea93b4ec0e5e3a6311fe89f936_238.png)

## 系统化预测与因子研究 🔬

![](img/b12499ea93b4ec0e5e3a6311fe89f936_240.png)

在搭建好回测系统后，我们就可以系统地研究和测试预测市场走势的因子。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_242.png)

### 因子研究流程

1.  **数据准备**：加载历史数据（如分钟线），并严格按时间排序。特别注意处理合约换月时的数据衔接问题。
2.  **划分数据集**：将数据按时间分为训练集、验证集和测试集（例如75%/25%），防止使用未来数据（Look-ahead Bias）。
3.  **构建因子**：计算一系列可能预测未来收益的特征。因子可以非常简单：
    ```python
    # 示例因子：过去5个Bar的平均收益率
    df[‘ret_5’] = df[‘close’].pct_change().rolling(5).mean()
    # 示例因子：当前成交量与过去20个Bar平均成交量的比值
    df[‘volume_ratio_20’] = df[‘volume’] / df[‘volume’].rolling(20).mean()
    ```
4.  **目标变量**：通常预测下一个Bar的收益率 `next_ret = df[‘close’].pct_change().shift(-1)`。
5.  **相关性分析**：计算每个因子与未来收益率之间的相关性。金融数据的信噪比很低，单个因子的相关性通常很弱（绝对值0.1已算不错）。
6.  **横截面验证**：在一个品种上有效的因子，需要在其他多个品种和历史不同阶段进行验证，以确认其稳健性。

### 因子灵感来源

*   **技术指标库**：如 `TA-Lib` 库中包含大量成熟的技术指标（SMA, RSI, MACD等），是很好的起点。
*   **学术论文与公开Alpha**：例如，WorldQuant发布的《101 Formulaic Alphas》论文，提供了101个量化因子公式，极具参考价值。
*   **市场观察**：仔细观察行情数据，寻找重复出现的模式（例如，放量上涨后常伴随短暂回调），并将其量化。
*   **另类数据**：在股票等领域，可以引入基本面、新闻情绪、社交媒体数据等。

**核心思想**：在量化交易中，**因子（特征）的质量往往比模型的选择更重要**。寻找独特、有效的数据和因子构建方法是获取超额收益的关键。

## 总结 🎯

本节课中我们一起学习了：
1.  **事件驱动回测框架**的完整工作流程，深入剖析了数据处理器、策略、执行引擎和投资组合四个核心模块的实现细节与交互逻辑。
2.  构建回测系统时应注意的**关键细节**，如数据管理、内存优化、严格的撮合逻辑设计以及止盈止损的实现思路。
3.  系统化进行**因子研究**的基本方法论，包括数据预处理、因子构建、相关性分析和横截面验证，并介绍了寻找因子灵感的多种途径。

![](img/b12499ea93b4ec0e5e3a6311fe89f936_244.png)

量化研究是一个需要耐心和细致的过程，从可靠的回测到有效的因子挖掘，每一步都需要亲手实践和反复验证。建议大家在后续的学习中，首先完成一个可运行的回测系统，然后开始尝试构建并测试自己的第一个交易因子。