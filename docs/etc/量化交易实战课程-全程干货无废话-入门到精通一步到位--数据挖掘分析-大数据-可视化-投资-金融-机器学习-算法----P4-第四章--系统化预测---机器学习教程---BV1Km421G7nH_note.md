# AI量化交易实战课程：P4：系统化预测 - 机器学习教程 📈

![](img/c83b19ae506b5175edeccb02a6b80452_0.png)

![](img/c83b19ae506b5175edeccb02a6b80452_1.png)

![](img/c83b19ae506b5175edeccb02a6b80452_2.png)

![](img/c83b19ae506b5175edeccb02a6b80452_3.png)

![](img/c83b19ae506b5175edeccb02a6b80452_4.png)

![](img/c83b19ae506b5175edeccb02a6b80452_5.png)

![](img/c83b19ae506b5175edeccb02a6b80452_6.png)

![](img/c83b19ae506b5175edeccb02a6b80452_7.png)

![](img/c83b19ae506b5175edeccb02a6b80452_8.png)

![](img/c83b19ae506b5175edeccb02a6b80452_9.png)

![](img/c83b19ae506b5175edeccb02a6b80452_10.png)

![](img/c83b19ae506b5175edeccb02a6b80452_11.png)

## 概述
在本节课中，我们将学习如何构建一个事件驱动的回测系统，并探讨如何系统化地挖掘和构建用于市场预测的因子。我们将从回顾回测框架的核心组件开始，然后深入讲解数据处理器、策略、投资组合和执行引擎的实现细节。最后，我们将介绍因子研究的基本方法，为后续的机器学习预测模型打下基础。

## 回顾：事件驱动回测框架 🔄

上一节我们介绍了量化交易的基本概念，本节中我们来看看事件驱动回测系统的核心架构。整个系统基于一个双层循环结构。

![](img/c83b19ae506b5175edeccb02a6b80452_13.png)

![](img/c83b19ae506b5175edeccb02a6b80452_15.png)

![](img/c83b19ae506b5175edeccb02a6b80452_17.png)

![](img/c83b19ae506b5175edeccb02a6b80452_19.png)

![](img/c83b19ae506b5175edeccb02a6b80452_21.png)

![](img/c83b19ae506b5175edeccb02a6b80452_23.png)

![](img/c83b19ae506b5175edeccb02a6b80452_25.png)

### 核心循环与事件队列
系统最外层是一个 `while` 循环，它持续运行直到市场数据引擎处理完所有数据。内层是一个 `while True` 循环，它从一个事件队列中不断取出并处理事件。

![](img/c83b19ae506b5175edeccb02a6b80452_27.png)

![](img/c83b19ae506b5175edeccb02a6b80452_29.png)

![](img/c83b19ae506b5175edeccb02a6b80452_31.png)

![](img/c83b19ae506b5175edeccb02a6b80452_33.png)

我们使用一个事件队列来管理所有待处理的事件。以下是处理流程的核心逻辑：

![](img/c83b19ae506b5175edeccb02a6b80452_35.png)

![](img/c83b19ae506b5175edeccb02a6b80452_37.png)

![](img/c83b19ae506b5175edeccb02a6b80452_39.png)

```python
while True:
    try:
        event = event_queue.get(block=False)
    except queue.Empty:
        break

    if event.type == 'BAR':
        strategy.on_bar(event)
    elif event.type == 'SIGNAL':
        portfolio.on_signal(event)
    elif event.type == 'ORDER':
        execution.on_order(event)
    elif event.type == 'FILL':
        portfolio.on_fill(event)
```

![](img/c83b19ae506b5175edeccb02a6b80452_41.png)

![](img/c83b19ae506b5175edeccb02a6b80452_43.png)

![](img/c83b19ae506b5175edeccb02a6b80452_45.png)

当事件队列为空时，意味着由当前市场数据（Bar）触发的一系列事件（信号、订单、成交）已全部处理完毕，系统将回到外层循环，获取下一个Bar。

![](img/c83b19ae506b5175edeccb02a6b80452_47.png)

![](img/c83b19ae506b5175edeccb02a6b80452_49.png)

![](img/c83b19ae506b5175edeccb02a6b80452_51.png)

![](img/c83b19ae506b5175edeccb02a6b80452_53.png)

![](img/c83b19ae506b5175edeccb02a6b80452_55.png)

![](img/c83b19ae506b5175edeccb02a6b80452_57.png)

### 主要组件与事件流
整个系统包含四个核心类，它们各自处理特定类型的事件：

![](img/c83b19ae506b5175edeccb02a6b80452_59.png)

![](img/c83b19ae506b5175edeccb02a6b80452_61.png)

![](img/c83b19ae506b5175edeccb02a6b80452_63.png)

![](img/c83b19ae506b5175edeccb02a6b80452_65.png)

![](img/c83b19ae506b5175edeccb02a6b80452_67.png)

1.  **市场数据引擎**：负责加载数据并发布 `BAR` 事件。
2.  **策略类**：接收 `BAR` 事件，计算并发布 `SIGNAL` 事件。
3.  **投资组合类**：接收 `SIGNAL` 事件，根据规则生成 `ORDER` 事件；同时接收 `FILL` 事件，更新持仓和交易记录。
4.  **执行引擎**：接收 `ORDER` 事件，根据市场行情进行撮合，生成 `FILL` 事件。

![](img/c83b19ae506b5175edeccb02a6b80452_69.png)

![](img/c83b19ae506b5175edeccb02a6b80452_71.png)

![](img/c83b19ae506b5175edeccb02a6b80452_73.png)

![](img/c83b19ae506b5175edeccb02a6b80452_75.png)

![](img/c83b19ae506b5175edeccb02a6b80452_77.png)

回测结束时，系统需要执行清理工作，例如撤销所有未成交订单，并强制平掉所有未平仓头寸，以确保最终账户状态为纯现金。

![](img/c83b19ae506b5175edeccb02a6b80452_79.png)

![](img/c83b19ae506b5175edeccb02a6b80452_81.png)

![](img/c83b19ae506b5175edeccb02a6b80452_82.png)

![](img/c83b19ae506b5175edeccb02a6b80452_83.png)

![](img/c83b19ae506b5175edeccb02a6b80452_84.png)

![](img/c83b19ae506b5175edeccb02a6b80452_85.png)

![](img/c83b19ae506b5175edeccb02a6b80452_86.png)

![](img/c83b19ae506b5175edeccb02a6b80452_88.png)

![](img/c83b19ae506b5175edeccb02a6b80452_90.png)

## 详解核心组件 ⚙️

![](img/c83b19ae506b5175edeccb02a6b80452_91.png)

![](img/c83b19ae506b5175edeccb02a6b80452_93.png)

![](img/c83b19ae506b5175edeccb02a6b80452_95.png)

![](img/c83b19ae506b5175edeccb02a6b80452_97.png)

![](img/c83b19ae506b5175edeccb02a6b80452_99.png)

![](img/c83b19ae506b5175edeccb02a6b80452_101.png)

![](img/c83b19ae506b5175edeccb02a6b80452_102.png)

![](img/c83b19ae506b5175edeccb02a6b80452_104.png)

理解了整体框架后，我们逐一深入各个核心组件的实现细节。

![](img/c83b19ae506b5175edeccb02a6b80452_106.png)

![](img/c83b19ae506b5175edeccb02a6b80452_108.png)

![](img/c83b19ae506b5175edeccb02a6b80452_110.png)

![](img/c83b19ae506b5175edeccb02a6b80452_112.png)

### 1. 数据处理器
数据处理器负责从数据源加载历史行情数据，并按时间顺序推送到事件队列中。

![](img/c83b19ae506b5175edeccb02a6b80452_114.png)

![](img/c83b19ae506b5175edeccb02a6b80452_116.png)

![](img/c83b19ae506b5175edeccb02a6b80452_118.png)

![](img/c83b19ae506b5175edeccb02a6b80452_120.png)

![](img/c83b19ae506b5175edeccb02a6b80452_122.png)

![](img/c83b19ae506b5175edeccb02a6b80452_124.png)

以下是需要注意的关键点：
*   **数据存储**：建议将清洗整理后的数据存入数据库，便于多品种、长时间序列的回测。初期可使用CSV文件，但需按合约、频率组织好目录结构。
*   **内存管理**：使用生成器逐条`yield`数据，避免一次性将全部数据加载到内存。
*   **数据缓存**：使用双端队列缓存最近一定数量的Bar，方便策略计算移动平均等需要历史数据的指标。
*   **合约切换**：当主力合约切换时，需要清空缓存，避免使用错误合约的历史数据。

![](img/c83b19ae506b5175edeccb02a6b80452_126.png)

![](img/c83b19ae506b5175edeccb02a6b80452_128.png)

![](img/c83b19ae506b5175edeccb02a6b80452_130.png)

![](img/c83b19ae506b5175edeccb02a6b80452_132.png)

![](img/c83b19ae506b5175edeccb02a6b80452_134.png)

![](img/c83b19ae506b5175edeccb02a6b80452_135.png)

### 2. 策略类
策略类是实现交易逻辑的核心。每个策略都必须实现 `on_bar` 方法。

![](img/c83b19ae506b5175edeccb02a6b80452_137.png)

![](img/c83b19ae506b5175edeccb02a6b80452_139.png)

![](img/c83b19ae506b5175edeccb02a6b80452_141.png)

![](img/c83b19ae506b5175edeccb02a6b80452_143.png)

以双均线策略为例，其核心是计算长短周期均线并产生交易信号：

![](img/c83b19ae506b5175edeccb02a6b80452_145.png)

![](img/c83b19ae506b5175edeccb02a6b80452_147.png)

![](img/c83b19ae506b5175edeccb02a6b80452_149.png)

![](img/c83b19ae506b5175edeccb02a6b80452_151.png)

![](img/c83b19ae506b5175edeccb02a6b80452_153.png)

![](img/c83b19ae506b5175edeccb02a6b80452_155.png)

```python
def on_bar(self, bar_event):
    # 更新价格序列
    self.symbol_data[bar_event.symbol].append(bar_event.close_price)

    # 计算均线
    if len(self.symbol_data[bar_event.symbol]) >= self.long_window:
        short_ma = np.mean(self.symbol_data[bar_event.symbol][-self.short_window:])
        long_ma = np.mean(self.symbol_data[bar_event.symbol][-self.long_window:])

        # 产生信号
        if short_ma > long_ma and not self.hold_long:
            signal = SignalEvent(bar_event.symbol, bar_event.datetime, 'LONG')
            self.events.put(signal)
        elif short_ma < long_ma and self.hold_long:
            signal = SignalEvent(bar_event.symbol, bar_event.datetime, 'EXIT')
            self.events.put(signal)
```

![](img/c83b19ae506b5175edeccb02a6b80452_157.png)

![](img/c83b19ae506b5175edeccb02a6b80452_159.png)

![](img/c83b19ae506b5175edeccb02a6b80452_161.png)

![](img/c83b19ae506b5175edeccb02a6b80452_163.png)

![](img/c83b19ae506b5175edeccb02a6b80452_165.png)

![](img/c83b19ae506b5175edeccb02a6b80452_167.png)

![](img/c83b19ae506b5175edeccb02a6b80452_169.png)

![](img/c83b19ae506b5175edeccb02a6b80452_171.png)

![](img/c83b19ae506b5175edeccb02a6b80452_173.png)

![](img/c83b19ae506b5175edeccb02a6b80452_175.png)

### 3. 执行引擎
执行引擎模拟交易所的撮合功能。它接收订单，并根据设定的规则判断是否成交。

![](img/c83b19ae506b5175edeccb02a6b80452_177.png)

![](img/c83b19ae506b5175edeccb02a6b80452_179.png)

![](img/c83b19ae506b5175edeccb02a6b80452_181.png)

![](img/c83b19ae506b5175edeccb02a6b80452_183.png)

![](img/c83b19ae506b5175edeccb02a6b80452_185.png)

![](img/c83b19ae506b5175edeccb02a6b80452_187.png)

![](img/c83b19ae506b5175edeccb02a6b80452_189.png)

![](img/c83b19ae506b5175edeccb02a6b80452_191.png)

![](img/c83b19ae506b5175edeccb02a6b80452_193.png)

![](img/c83b19ae506b5175edeccb02a6b80452_195.png)

一个严格且常见的限价单撮合逻辑（以买单为例）需要同时满足两个条件：
1.  订单价格 `<=` 上一根Bar的收盘卖价。
2.  订单价格 `>=` 当前Bar的最低价。

![](img/c83b19ae506b5175edeccb02a6b80452_197.png)

![](img/c83b19ae506b5175edeccb02a6b80452_199.png)

![](img/c83b19ae506b5175edeccb02a6b80452_201.png)

![](img/c83b19ae506b5175edeccb02a6b80452_203.png)

![](img/c83b19ae506b5175edeccb02a6b80452_205.png)

![](img/c83b19ae506b5175edeccb02a6b80452_207.png)

**公式**：`(order_price <= last_bar.close_ask) && (order_price >= this_bar.low_ask)`

![](img/c83b19ae506b5175edeccb02a6b80452_209.png)

这种设计旨在模拟“价格下穿”时订单被触发的场景，比简单的“订单价格优于当前最低价”更严格，能减少回测中的乐观偏差。开发者可以根据策略类型调整撮合逻辑。

![](img/c83b19ae506b5175edeccb02a6b80452_211.png)

![](img/c83b19ae506b5175edeccb02a6b80452_213.png)

### 4. 投资组合类
投资组合类是最复杂的组件，它连接了策略信号和订单执行。

![](img/c83b19ae506b5175edeccb02a6b80452_215.png)

![](img/c83b19ae506b5175edeccb02a6b80452_217.png)

![](img/c83b19ae506b5175edeccb02a6b80452_218.png)

它主要完成两项工作：
*   **信号处理**：在 `on_signal` 方法中，根据接收到的信号、当前持仓、风控规则等，决定下单的方向、价格和数量，并创建订单事件。
*   **成交处理**：在 `on_fill` 方法中，根据成交回报更新持仓、现金，并记录完整的交易记录。

![](img/c83b19ae506b5175edeccb02a6b80452_220.png)

![](img/c83b19ae506b5175edeccb02a6b80452_222.png)

![](img/c83b19ae506b5175edeccb02a6b80452_224.png)

![](img/c83b19ae506b5175edeccb02a6b80452_225.png)

![](img/c83b19ae506b5175edeccb02a6b80452_227.png)

![](img/c83b19ae506b5175edeccb02a6b80452_228.png)

进阶功能如止盈止损，可以在此类中实现。其本质是为每一笔开仓交易挂上两个反向的平仓订单（止盈单和止损单），并在价格触发时成交。移动止损则需要根据最新的市场价格动态更新止损单的价格。

![](img/c83b19ae506b5175edeccb02a6b80452_230.png)

![](img/c83b19ae506b5175edeccb02a6b80452_232.png)

## 系统化预测：因子研究 🔍

构建好回测系统后，我们就可以系统地研究和测试预测市场走势的因子。

### 因子研究概述
因子是用于预测未来收益率（或其他目标变量）的特征或指标。在量化交易中，因子研究的目标是找到与未来资产价格变动具有稳定相关性的特征。

### 因子构建流程
以下是进行因子研究的基本步骤：

1.  **准备数据**：加载历史数据，并严格按时间排序。注意处理合约换月带来的数据断层问题。
2.  **划分数据集**：将数据按时间分为训练集、验证集和测试集，避免使用未来数据。
3.  **计算因子值**：基于历史数据，计算一系列候选因子的值。例如：
    *   技术指标（移动平均线、RSI、布林带等）
    *   量价关系（成交量变化、成交额）
    *   历史收益（过去N周期的平均收益率）
    *   波动性指标
4.  **计算目标变量**：通常使用下一期的收益率 `Return(t+1)` 作为预测目标。
5.  **评估因子有效性**：计算每个因子与未来收益率之间的相关性。在金融数据中，即使相关性绝对值达到0.1，也可能是一个有意义的信号。

### 因子来源与挖掘
初学者可以从以下途径寻找因子灵感：

*   **技术指标库**：如TA-Lib库，包含了大量成熟的技术指标，是很好的起点。
*   **学术论文与公开研究**：例如World Quant发布的《101 Formulaic Alphas》论文，提供了101个阿尔法因子公式。
*   **观察市场**：通过观察盘口、成交量、价格形态的Pattern，自己构思因子逻辑。

### 注意事项
*   **过拟合风险**：在有限的数据上测试过多的因子，很容易找到偶然性相关的“伪信号”。需要通过样本外测试和多个品种验证来规避。
*   **因子衰减**：有效的因子会随着被市场参与者广泛知晓而逐渐失效，需要持续研究和更新因子库。
*   **数据质量**：垃圾进，垃圾出。确保基础数据的准确性和清洁度是因子有效的前提。

![](img/c83b19ae506b5175edeccb02a6b80452_234.png)

## 总结
本节课中我们一起学习了事件驱动回测系统的完整构建方法，深入剖析了数据、策略、执行和投资组合四大核心模块的职责与交互。我们还介绍了系统化因子研究的基本框架，从因子的概念、构建、评估到挖掘来源，为后续利用机器学习模型进行预测打下了坚实的基础。记住，一个稳健的回测系统是检验想法的基石，而持续、系统的因子研究则是产生阿尔法的源泉。