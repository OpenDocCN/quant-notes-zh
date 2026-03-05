# 量化金融与机器学习：P1：简单的均线策略实现 🚀

![](img/15a29eeaa698616297b9ab779f80234c_1.png)

![](img/15a29eeaa698616297b9ab779f80234c_3.png)

![](img/15a29eeaa698616297b9ab779f80234c_5.png)

在本节课中，我们将学习如何使用 Backtrader 框架实现一个简单的均线突破交易策略。我们将从环境配置讲起，逐步构建策略逻辑，并最终运行回测查看结果。

## 环境配置与关键概念 🔧

在具体实现策略之前，有几个关键点需要提前了解和配置。

![](img/15a29eeaa698616297b9ab779f80234c_7.png)

### 股票池大小调整

![](img/15a29eeaa698616297b9ab779f80234c_9.png)

![](img/15a29eeaa698616297b9ab779f80234c_11.png)

![](img/15a29eeaa698616297b9ab779f80234c_13.png)

如果你的电脑内存有限，建议使用较小的股票池。这可以减少内存占用，使程序运行更流畅。

![](img/15a29eeaa698616297b9ab779f80234c_15.png)

以下是两种股票池的配置方式：
*   **全量股票池**：包含所有股票，但可能占用1-2GB内存。
*   **部分股票池**：通过设置参数（例如 `limit=2500`）来限制股票数量，内存占用更小。

![](img/15a29eeaa698616297b9ab779f80234c_17.png)

![](img/15a29eeaa698616297b9ab779f80234c_19.png)

![](img/15a29eeaa698616297b9ab779f80234c_21.png)

为了演示方便，我们选择部分股票池，并确保比亚迪（股票代码在2500以内）被包含在内，以便后续举例。

### 数据空值处理机制

![](img/15a29eeaa698616297b9ab779f80234c_23.png)

![](img/15a29eeaa698616297b9ab779f80234c_25.png)

![](img/15a29eeaa698616297b9ab779f80234c_27.png)

![](img/15a29eeaa698616297b9ab779f80234c_29.png)

Backtrader 要求股票池中的每只股票在每个交易日都有数据。但现实中，股票会因停牌等原因出现数据空值。因此，我们需要一个容错机制来处理这些空值。

![](img/15a29eeaa698616297b9ab779f80234c_31.png)

![](img/15a29eeaa698616297b9ab779f80234c_33.png)

![](img/15a29eeaa698616297b9ab779f80234c_35.png)

![](img/15a29eeaa698616297b9ab779f80234c_37.png)

我们使用以下方法进行填补：
```python
# 使用前后价格填补空值
data.fillna(method='bfill', inplace=True)  # 优先用后面的价格填补
data.fillna(method='ffill', inplace=True)  # 如果后面没有，再用前面的价格填补
```
这种方法虽然在某些场景下不够严谨，但能确保策略不会因数据缺失而出现重大错误。对于初学者，建议先使用这种简单方式。

![](img/15a29eeaa698616297b9ab779f80234c_39.png)

![](img/15a29eeaa698616297b9ab779f80234c_41.png)

![](img/15a29eeaa698616297b9ab779f80234c_43.png)

![](img/15a29eeaa698616297b9ab779f80234c_45.png)

### Backtrader 的交易执行机制

![](img/15a29eeaa698616297b9ab779f80234c_47.png)

![](img/15a29eeaa698616297b9ab779f80234c_49.png)

![](img/15a29eeaa698616297b9ab779f80234c_51.png)

这是一个非常重要的概念。在 Backtrader 中，当你在策略中发出一个交易信号（例如在日期 `t` 买入），实际的交易并不会在当天执行。

![](img/15a29eeaa698616297b9ab779f80234c_53.png)

![](img/15a29eeaa698616297b9ab779f80234c_55.png)

![](img/15a29eeaa698616297b9ab779f80234c_57.png)

![](img/15a29eeaa698616297b9ab779f80234c_58.png)

**实际交易发生在信号日 `t` 的下一个交易日**，并且使用的是下一个交易日的开盘价。理解这一点对于准确评估策略表现至关重要。

![](img/15a29eeaa698616297b9ab779f80234c_60.png)

![](img/15a29eeaa698616297b9ab779f80234c_61.png)

![](img/15a29eeaa698616297b9ab779f80234c_63.png)

![](img/15a29eeaa698616297b9ab779f80234c_65.png)

![](img/15a29eeaa698616297b9ab779f80234c_66.png)

上一节我们介绍了环境配置和关键概念，本节中我们来看看如何具体实现均线突破策略。

![](img/15a29eeaa698616297b9ab779f80234c_68.png)

![](img/15a29eeaa698616297b9ab779f80234c_70.png)

![](img/15a29eeaa698616297b9ab779f80234c_72.png)

![](img/15a29eeaa698616297b9ab779f80234c_73.png)

## 均线突破策略逻辑与实现 📈

![](img/15a29eeaa698616297b9ab779f80234c_74.png)

![](img/15a29eeaa698616297b9ab779f80234c_75.png)

![](img/15a29eeaa698616297b9ab779f80234c_76.png)

![](img/15a29eeaa698616297b9ab779f80234c_78.png)

![](img/15a29eeaa698616297b9ab779f80234c_80.png)

我们将实现一个经典的交易策略：当股价上穿30日均线时买入，当股价下穿30日均线时卖出。

![](img/15a29eeaa698616297b9ab779f80234c_82.png)

![](img/15a29eeaa698616297b9ab779f80234c_84.png)

### 策略核心逻辑

![](img/15a29eeaa698616297b9ab779f80234c_86.png)

![](img/15a29eeaa698616297b9ab779f80234c_88.png)

![](img/15a29eeaa698616297b9ab779f80234c_90.png)

策略的数学表达很简单：
*   **买入信号**：`close_price > ma_30`
*   **卖出信号**：`close_price < ma_30`

![](img/15a29eeaa698616297b9ab779f80234c_91.png)

![](img/15a29eeaa698616297b9ab779f80234c_93.png)

![](img/15a29eeaa698616297b9ab779f80234c_94.png)

但这里有一个问题：股价可能长时间位于均线之上或之下，我们不需要在此期间重复发出交易信号。我们只需要在状态发生**切换**时（即从“线下”变为“线上”，或从“线上”变为“线下”）触发一次交易即可。

![](img/15a29eeaa698616297b9ab779f80234c_96.png)

![](img/15a29eeaa698616297b9ab779f80234c_98.png)

![](img/15a29eeaa698616297b9ab779f80234c_100.png)

![](img/15a29eeaa698616297b9ab779f80234c_102.png)

因此，我们需要一个状态变量来记录当前是否持有仓位，以避免重复交易。

![](img/15a29eeaa698616297b9ab779f80234c_104.png)

![](img/15a29eeaa698616297b9ab779f80234c_106.png)

![](img/15a29eeaa698616297b9ab779f80234c_108.png)

![](img/15a29eeaa698616297b9ab779f80234c_110.png)

### 代码实现步骤

![](img/15a29eeaa698616297b9ab779f80234c_112.png)

![](img/15a29eeaa698616297b9ab779f80234c_114.png)

![](img/15a29eeaa698616297b9ab779f80234c_116.png)

以下是实现该策略的主要步骤：

![](img/15a29eeaa698616297b9ab779f80234c_118.png)

![](img/15a29eeaa698616297b9ab779f80234c_120.png)

1.  **准备数据**：获取目标股票（例如比亚迪）的收盘价数据，并计算其30日移动平均线（MA30）。
    ```python
    # 获取比亚迪收盘价
    close_price = data['002594.SZ']['close']
    # 计算30日移动平均线
    ma_30 = close_price.rolling(window=30).mean()
    ```

![](img/15a29eeaa698616297b9ab779f80234c_122.png)

2.  **可视化**：可以绘制收盘价和均线图，直观观察其关系。
    ```python
    import matplotlib.pyplot as plt
    plt.figure(figsize=(14, 7))
    plt.plot(close_price.index, close_price, label='Close Price')
    plt.plot(ma_30.index, ma_30, label='30-Day MA')
    plt.legend()
    plt.show()
    ```

![](img/15a29eeaa698616297b9ab779f80234c_124.png)

![](img/15a29eeaa698616297b9ab779f80234c_126.png)

![](img/15a29eeaa698616297b9ab779f80234c_128.png)

![](img/15a29eeaa698616297b9ab779f80234c_130.png)

3.  **构建信号逻辑**：遍历数据，在状态切换点生成交易信号列表。
    ```python
    # 初始化状态变量，0表示未持仓，1表示已持仓
    position = 0
    buy_signals = []
    sell_signals = []

    # 从第30个数据点开始遍历（因为MA30前29个值为NaN）
    for i in range(30, len(close_price)):
        current_price = close_price.iloc[i]
        current_ma = ma_30.iloc[i]
        current_date = close_price.index[i]

        # 生成买入信号：价格上穿均线 且 当前未持仓
        if current_price > current_ma and position == 0:
            buy_signals.append((current_date, '002594.SZ'))
            position = 1 # 状态变为已持仓
        # 生成卖出信号：价格下穿均线 且 当前已持仓
        elif current_price < current_ma and position == 1:
            sell_signals.append((current_date, '002594.SZ'))
            position = 0 # 状态变为未持仓
    ```

![](img/15a29eeaa698616297b9ab779f80234c_132.png)

![](img/15a29eeaa698616297b9ab779f80234c_134.png)

![](img/15a29eeaa698616297b9ab779f80234c_136.png)

![](img/15a29eeaa698616297b9ab779f80234c_138.png)

4.  **整合到Backtrader框架**：将生成的买入(`buy_signals`)和卖出(`sell_signals`)信号列表，传入我们预先封装好的Backtrader回测执行函数中，进行回测分析。

![](img/15a29eeaa698616297b9ab779f80234c_140.png)

![](img/15a29eeaa698616297b9ab779f80234c_142.png)

![](img/15a29eeaa698616297b9ab779f80234c_143.png)

![](img/15a29eeaa698616297b9ab779f80234c_144.png)

## 策略回测与结果分析 📊

![](img/15a29eeaa698616297b9ab779f80234c_146.png)

![](img/15a29eeaa698616297b9ab779f80234c_148.png)

![](img/15a29eeaa698616297b9ab779f80234c_150.png)

![](img/15a29eeaa698616297b9ab779f80234c_152.png)

运行回测后，我们可以观察策略在比亚迪股票（2020-2023年）上的表现。

![](img/15a29eeaa698616297b9ab779f80234c_154.png)

![](img/15a29eeaa698616297b9ab779f80234c_156.png)

![](img/15a29eeaa698616297b9ab779f80234c_158.png)

![](img/15a29eeaa698616297b9ab779f80234c_160.png)

**结果分析**：
在这个时间段内，比亚迪股价本身有显著上涨。然而，这个简单的均线突破策略并没有创造出额外的超额收益，其表现甚至可能不如简单的买入并持有策略。

![](img/15a29eeaa698616297b9ab779f80234c_162.png)

![](img/15a29eeaa698616297b9ab779f80234c_164.png)

![](img/15a29eeaa698616297b9ab779f80234c_166.png)

![](img/15a29eeaa698616297b9ab779f80234c_168.png)

这说明了几个问题：
1.  策略的有效性依赖于市场环境和标的特性。
2.  简单的技术指标策略可能需要结合其他因子或进行参数优化。
3.  回测是检验策略想法的重要工具，但一个策略在历史数据上表现一般，是其开发过程中的正常现象。

![](img/15a29eeaa698616297b9ab779f80234c_170.png)

![](img/15a29eeaa698616297b9ab779f80234c_172.png)

![](img/15a29eeaa698616297b9ab779f80234c_174.png)

![](img/15a29eeaa698616297b9ab779f80234c_176.png)

本节课中我们一起学习了如何配置Backtrader环境、理解了其交易执行机制，并亲手实现了一个完整的均线突破策略。虽然这个策略在示例中表现平平，但它为我们搭建了一个标准的策略开发流程，这是探索更复杂量化模型的基础。