# Python量化交易教程：7.1：价格突破策略与动态交易管理

![](img/9af05b7813ec5ab58eef823c45450f22_0.png)

![](img/9af05b7813ec5ab58eef823c45450f22_2.png)

在本节课中，我们将学习如何使用Python回测一个基于价格突破的交易策略。我们将应用三种不同的交易管理方法，并观察它们如何将策略的利润从约10%提升至近100%。课程内容适用于手动交易和算法交易。

## 数据准备与指标计算

首先，我们需要准备数据和计算必要的技术指标。我们将使用2003年至2023年的欧元/美元日线数据。

以下是数据加载和预处理的核心步骤：

```python
import pandas as pd
import pandas_ta as ta

# 加载数据并转换为Pandas DataFrame
df = pd.read_csv(‘your_data.csv’)
df.rename(columns={‘Open’: ‘open’, ‘High’: ‘high’, ‘Low’: ‘low’, ‘Close’: ‘close’, ‘Volume’: ‘volume’}, inplace=True)

![](img/9af05b7813ec5ab58eef823c45450f22_4.png)

# 清理数据：移除交易量为零的行（如节假日）
df = df[df[‘volume’] != 0]
df.reset_index(drop=True, inplace=True)

# 计算50周期指数移动平均线（EMA）
df[‘ema’] = ta.ema(df[‘close’], length=50)
```

上一节我们介绍了数据准备，本节中我们来看看如何利用移动平均线判断趋势。

我们通过检查连续10根K线相对于EMA的位置来定义趋势方向：

```python
def detect_trend(row):
    # 假设我们检查当前K线及前9根K线（共10根）
    lookback = 10
    idx = row.name
    if idx < lookback:
        return 0
    recent_candles = df.loc[idx-lookback+1:idx, ‘close’]
    ema_values = df.loc[idx-lookback+1:idx, ‘ema’]
    if all(recent_candles > ema_values):
        return 2  # 上升趋势
    elif all(recent_candles < ema_values):
        return 1  # 下降趋势
    else:
        return 0  # 无明确趋势

df[‘ema_signal’] = df.apply(detect_trend, axis=1)
```

## 识别关键价位（枢轴点）

为了识别支撑和阻力位，我们需要找到图表中的局部高点和低点（枢轴点）。

以下是识别枢轴点的函数：

```python
def is_pivot(candle_index, window=6):
    """
    判断给定索引的K线是否为枢轴点（局部高点或低点）。
    window: 在两侧检查的邻居K线数量。
    """
    if candle_index < window or candle_index + window >= len(df):
        return 0
    pivot_low = 1
    pivot_high = 2
    current_low = df.iloc[candle_index][‘low’]
    current_high = df.iloc[candle_index][‘high’]
    # 检查是否为低点
    for i in range(candle_index - window, candle_index + window + 1):
        if df.iloc[i][‘low’] < current_low:
            pivot_low = 0
        if df.iloc[i][‘high’] > current_high:
            pivot_high = 0
    if pivot_low and pivot_high:
        return 3
    elif pivot_low:
        return 1
    elif pivot_high:
        return 2
    else:
        return 0

![](img/9af05b7813ec5ab58eef823c45450f22_6.png)

![](img/9af05b7813ec5ab58eef823c45450f22_7.png)

# 应用函数
df[‘pivot’] = [is_pivot(i) for i in range(len(df))]
```

![](img/9af05b7813ec5ab58eef823c45450f22_9.png)

## 检测突破信号

![](img/9af05b7813ec5ab58eef823c45450f22_11.png)

核心策略是检测价格在关键水平（支撑/阻力）的突破。我们寻找价格在同一水平附近反弹三次后发生突破的模式。

以下是检测突破模式的逻辑概要：

1.  识别由三个或更多枢轴点构成的水平区域。
2.  确认价格在突破前曾从该水平反弹。
3.  当收盘价突破该水平，且趋势方向（由EMA判断）与突破方向一致时，产生交易信号。
    *   向下突破支撑位：卖出信号（信号值=1）
    *   向上突破阻力位：买入信号（信号值=2）

我们将此检测函数应用于整个DataFrame，结果保存在`‘detected_pattern’`列中。

## 回测策略与交易管理

现在，我们进入核心的回测部分。我们将使用`backtesting.py`库，并测试三种交易管理方法。

### 方法一：固定止损止盈

这是最基本的方法，为所有交易设置固定的止损和止盈距离。例如，设置止损为入场价的3%，止盈为止损距离的2倍。

以下是策略类的部分代码框架：

```python
from backtesting import Strategy

class BreakoutStrategy(Strategy):
    # 固定参数
    stop_loss_pct = 0.03  # 止损百分比
    tp_sl_ratio = 2       # 止盈止损比率

    def init(self):
        # 将信号列（detected_pattern）设置为指标
        self.signal = self.I(lambda: self.data.detected_pattern)

    def next(self):
        current_price = self.data.close[-1]
        if len(self.trades) == 0:  # 无未平仓交易
            if self.signal[-1] == 2:  # 买入信号
                sl_price = current_price * (1 - self.stop_loss_pct)
                sl_distance = abs(current_price - sl_price)
                tp_price = current_price + (sl_distance * self.tp_sl_ratio)
                self.buy(sl=sl_price, tp=tp_price)
            elif self.signal[-1] == 1:  # 卖出信号
                # 类似地设置空头止损止盈...
                pass
```

使用此方法，在测试期内策略获得了约20%的总回报，胜率为57%，盈亏比为2。但交易信号较少。

### 方法二：基于RSI的动态出场

为了更动态地管理交易，我们引入相对强弱指数（RSI）作为出场条件。例如，在持有多头时，当RSI超过80时平仓；持有空头时，当RSI低于20时平仓。

我们可以在`next`方法中增加以下逻辑：

```python
    def next(self):
        current_rsi = self.data.rsi[-1]
        for trade in self.trades:
            if trade.is_long and current_rsi > 80:
                trade.close()
            elif trade.is_short and current_rsi < 20:
                trade.close()
        # ... 原有的开仓逻辑
```

此方法的表现因参数而异，有时可能不如固定止损止盈稳定，但提供了更灵活的市场适应性。

### 方法三：分批止盈与移动止损

这是一种更高级的管理技术，旨在锁定部分利润并降低风险。

以下是该方法的操作步骤：

1.  将初始头寸分为两半。
2.  为第一半头寸设置一个较近的止盈目标（例如，止损距离的1.5倍）。
3.  为第二半头寸设置一个较远的止盈目标（例如，止损距离的2倍）。
4.  当价格达到第一个止盈目标时，平仓一半头寸，并将剩余头寸的止损移动至入场价（盈亏平衡点）。
5.  如果价格继续有利方向运动并触及第二个止盈目标，则平仓剩余头寸，获得全额利润。

这种方法的核心优势在于：它确保了一部分利润落袋为安，同时为剩余仓位保留了获取更大收益的机会，并通过移动止损有效控制了回撤。

在代码实现上，这相当于在产生信号时，连续执行两次`self.buy()`或`self.sell()`操作，但为它们分配不同的止盈目标和一半的头寸规模。同时，需要在第一个止盈触发后，通过跟踪交易ID来修改剩余仓位的止损价。

通过优化头寸规模（例如，使用账户资金的更高比例）并结合此方法，在测试中策略总回报提升至约99%，胜率提高至61%。然而，最大回撤也有所增加，这表明在追求更高收益的同时，风险也相应增大。

![](img/9af05b7813ec5ab58eef823c45450f22_13.png)

## 总结与优化建议

![](img/9af05b7813ec5ab58eef823c45450f22_15.png)

本节课中我们一起学习了如何构建并回测一个价格突破策略，并重点比较了三种交易管理方法：

1.  **固定止损止盈**：简单稳定，盈亏比好，但信号频率和适应性是挑战。
2.  **RSI动态出场**：增加了对市场状态的响应，但需要精细优化参数，否则可能增加不确定性。
3.  **分批止盈与移动止损**：能显著提升盈利潜力并管理下行风险，但可能导致回撤增加，且实现逻辑稍复杂。

![](img/9af05b7813ec5ab58eef823c45450f22_17.png)

![](img/9af05b7813ec5ab58eef823c45450f22_19.png)

需要强调的是，本教程展示的结果基于特定的市场（欧元/美元）、时间框架（日线）和一组固定参数（如3%止损、2倍盈亏比、3次反弹突破）。这并非一个“快速致富”方案，而是一个理解策略行为和交易管理威力的范例。

为了在实际应用中取得更好效果，您可以考虑以下优化方向：

*   **调整参数**：尝试不同的止损百分比、止盈止损比率、EMA周期、RSI阈值和枢轴点窗口。
*   **更换市场与时间框架**：在更短的时间框架（如4小时或1小时图）上测试，可能会产生更频繁的交易信号。
*   **添加过滤条件**：结合其他指标（如波动率、成交量）过滤假突破。
*   **考虑交易成本**：在回测中加入佣金和滑点模型，使结果更贴近现实。
*   **参数优化**：使用网格搜索或优化算法寻找更优的参数组合。

![](img/9af05b7813ec5ab58eef823c45450f22_21.png)

![](img/9af05b7813ec5ab58eef823c45450f22_22.png)

通过不断实验和调整，您可以找到更适合自己风险偏好和交易风格的策略配置。祝您交易顺利！