# Python量化43：自动化RSI策略 - P1 - LuQuant

![](img/51210a0f0125f38c1e3b5d8b76a143dd_1.png)

在本教程中，我们将学习如何构建并测试一个基于RSI指标的自动化交易策略。我们将从策略的核心规则讲起，逐步深入到使用Python进行数据加载、指标计算、信号生成以及回溯测试的完整流程。教程将涵盖不同时间框架下的策略表现，并探讨多种交易管理方法（如固定止损止盈、ATR动态止损、追踪止损）对最终结果的影响。

## 📈 策略概述与核心规则

上一节我们介绍了本教程的目标，本节中我们来看看策略的核心逻辑。该策略结合了移动平均线（MA）判断趋势和相对强弱指数（RSI）寻找入场点。

以下是策略的具体规则：

1.  **趋势判断**：使用200周期指数移动平均线（EMA200）判断市场趋势。
    *   公式：`EMA200 = ta.ema(close, length=200)`
    *   若价格**高于**EMA200，则判定为**上升趋势**，此时只寻找**买入**机会。
    *   若价格**低于**EMA200，则判定为**下降趋势**，此时只寻找**卖出**机会。
2.  **入场信号**：在确定的趋势方向内，使用RSI指标寻找超买或超卖点作为入场信号。
    *   公式：`RSI = ta.rsi(close, length=RSI_length)`
    *   在**上升趋势**中，当RSI值**低于10**时，产生**买入**信号。
    *   在**下降趋势**中，当RSI值**高于90**时，产生**卖出**信号。
3.  **趋势确认**：为避免假突破，策略要求价格在EMA200上方或下方持续一定数量的K线（例如8根）以确认趋势有效。

## 🛠️ 数据准备与指标计算

理解了策略规则后，我们需要准备数据并计算必要的技术指标。本节将使用`pandas`和`pandas_ta`库来完成这些任务。

![](img/51210a0f0125f38c1e3b5d8b76a143dd_3.png)

首先，我们加载指定时间范围（如15分钟）的历史K线数据，并清理无效数据（如交易量为0的时段）。

```python
import pandas as pd
import pandas_ta as ta

# 假设df是包含‘open‘, ‘high‘, ‘low‘, ‘close‘, ‘volume‘列的DataFrame
# 清理数据
df = df[df[‘volume‘] != 0].reset_index(drop=True)
```

接着，我们计算策略所需的指标：200周期指数移动平均线（EMA200）、RSI以及平均真实波幅（ATR，用于后续的动态止损）。

```python
# 计算技术指标
df[‘ema200‘] = ta.ema(df[‘close‘], length=200)
df[‘rsi‘] = ta.rsi(df[‘close‘], length=3)  # RSI长度可根据时间框架调整
df[‘atr‘] = ta.atr(df[‘high‘], df[‘low‘], df[‘close‘], length=14)
```

## 🚦 生成交易信号

有了基础指标，我们现在可以编写逻辑来生成交易信号。这个过程分为两步：先判断趋势，再结合RSI生成最终的入场信号。

以下是生成趋势信号的代码逻辑：

```python
def check_trend(df, confirmation_bars=8):
    signals = []
    for i in range(len(df)):
        if i < confirmation_bars:
            signals.append(0)
            continue
        # 检查最近confirmation_bars根K线是否持续在EMA200之上
        bars_above = all(df[‘close‘].iloc[i-confirmation_bars:i] > df[‘ema200‘].iloc[i-confirmation_bars:i])
        # 检查最近confirmation_bars根K线是否持续在EMA200之下
        bars_below = all(df[‘close‘].iloc[i-confirmation_bars:i] < df[‘ema200‘].iloc[i-confirmation_bars:i])
        
        if bars_below:
            signals.append(1)  # 下降趋势
        elif bars_above:
            signals.append(2)  # 上升趋势
        else:
            signals.append(0)  # 无明确趋势
    return signals

df[‘ma_signal‘] = check_trend(df)
```

然后，我们结合趋势信号和RSI值，生成最终的买入（信号值2）或卖出（信号值1）信号。

```python
total_signal = []
for i in range(len(df)):
    ma_sig = df[‘ma_signal‘].iloc[i]
    rsi_val = df[‘rsi‘].iloc[i]
    
    if ma_sig == 1 and rsi_val >= 90:  # 下降趋势且超买
        total_signal.append(1)  # 卖出信号
    elif ma_sig == 2 and rsi_val <= 10: # 上升趋势且超卖
        total_signal.append(2)  # 买入信号
    else:
        total_signal.append(0)  # 无信号

df[‘total_signal‘] = total_signal
```

## 📊 策略可视化与初步检查

在开始正式的回溯测试之前，将信号可视化在图表上是一个好习惯，可以帮助我们直观地检查信号逻辑是否符合预期。

我们可以将买入和卖出信号以标记点的形式添加到K线图上。

```python
import plotly.graph_objects as go

# 为信号添加绘图坐标
df[‘plot_price‘] = 0
df.loc[df[‘total_signal‘] == 1, ‘plot_price‘] = df[‘high‘] * 1.001  # 卖出信号点画在最高价上方
df.loc[df[‘total_signal‘] == 2, ‘plot_price‘] = df[‘low‘] * 0.999   # 买入信号点画在最低价下方

# 绘制图表
fig = go.Figure(data=[go.Candlestick(x=df.index, open=df[‘open‘], high=df[‘high‘], low=df[‘low‘], close=df[‘close‘])])
fig.add_scatter(x=df.index, y=df[‘ema200‘], mode=‘lines‘, name=‘EMA200‘)
fig.add_scatter(x=df.index[df[‘total_signal‘]==1], y=df[‘plot_price‘][df[‘total_signal‘]==1],
                mode=‘markers‘, marker=dict(symbol=‘triangle-down‘, size=10, color=‘red‘), name=‘Sell Signal‘)
fig.add_scatter(x=df.index[df[‘total_signal‘]==2], y=df[‘plot_price‘][df[‘total_signal‘]==2],
                mode=‘markers‘, marker=dict(symbol=‘triangle-up‘, size=10, color=‘green‘), name=‘Buy Signal‘)
fig.show()
```

## ⚙️ 回溯测试与交易管理

策略信号看起来合理，接下来我们进入核心环节——回溯测试。本节将介绍三种不同的交易管理方法，并观察它们对策略绩效的影响。

回溯测试的基本设定如下：
*   初始资金：100美元
*   仓位大小：账户净值的20%
*   杠杆：1:50
*   每次只持有一个仓位

### 方法一：固定点数止损止盈

这是最简单的方法，为每笔交易设置固定的止损和止盈点数。

```python
stop_loss_pips = 45
take_profit_pips = 45
# 在回溯测试循环中，根据入场价和方向计算止损价和止盈价
```

在2019-2022年的15分钟数据上进行测试，结果如下：
*   总收益率：52%
*   胜率：52%
*   最大回撤：-62%

### 方法二：基于ATR的动态止损止盈

此方法使用平均真实波幅（ATR）来动态设置止损距离，并设置一个止盈止损比率。

```python
atr_multiplier_sl = 1.3
risk_reward_ratio = 1.3
# 止损距离 = ATR * atr_multiplier_sl
# 止盈距离 = 止损距离 * risk_reward_ratio
```

使用相同数据测试，结果显著改善：
*   总收益率：116%
*   胜率：49%
*   最大回撤：-17%

### 方法三：基于ATR的追踪止损

该方法使用一个随价格移动的追踪止损来保护利润。

```python
trailing_stop_atr_multiplier = 1.5
# 追踪止损距离 = ATR * trailing_stop_atr_multiplier
# 在持仓期间，不断更新追踪止损位
```

测试结果如下：
*   总收益率：67%
*   胜率：48%
*   最大回撤：-22%

## 🔍 策略稳健性检验

一个策略在特定时间段表现良好，可能只是运气。为了检验其稳健性，我们需要在不同的历史时期进行测试。

我们将策略应用到2016-2019年的数据上。结果发现：
*   **固定点数法**和**ATR动态法**在2018年之前几乎不盈利，净值长期横盘，之后才开始增长。
*   **追踪止损法**表现类似。

这表明该策略在近期（2018-2022年）的市场环境中更有效，可能存在对市场状态的依赖性。

## ⏱️ 不同时间框架下的表现

原策略设计用于1分钟图表，但我们也在15分钟和1小时框架上进行了测试。以下是简要结论：

*   **15分钟框架**：使用调整后的参数（如RSI长度=3），配合ATR动态止损止盈法，能取得最佳且稳定的正收益。
*   **1分钟框架**：数据量巨大，对计算资源要求高。在短周期测试中，策略表现不稳定，盈利困难。
*   **1小时框架**：策略整体表现不佳，三种管理方法均难以获得持续正收益。

**核心发现**：该RSI策略在15分钟时间框架下，结合ATR动态止损止盈（参数约为1.3倍ATR止损，1.3倍止盈止损比），在2018年后的市场中展现了较强的盈利能力。

## 📝 总结与建议

本节课中我们一起学习了如何构建、实现并全面测试一个基于RSI和移动平均线的自动化交易策略。我们经历了从规则定义、指标计算、信号生成、可视化到回溯测试和稳健性检验的完整流程。

关键要点总结：
1.  **多因子结合**：单纯的RSI超买超卖信号噪音很大，与趋势指标（如EMA200）结合能有效过滤假信号。
2.  **参数与时间框架**：策略参数（如RSI长度、趋势确认K线数）需要根据所选时间框架进行优化。本策略在15分钟图上表现最佳。
3.  **交易管理至关重要**：相同的入场信号，搭配不同的止损止盈管理方法，结果差异巨大。基于ATR的动态风险管理方法在本案例中显著优于固定点数法。
4.  **稳健性测试必不可少**：一个策略必须在不同市场时期（如牛市、熊市、震荡市）进行测试，以评估其普适性和潜在失效风险。
5.  **实盘前的准备**：在实盘使用任何策略前，应在最新、样本外的数据上进行前瞻性测试，并做好资金管理和风险控制。

![](img/51210a0f0125f38c1e3b5d8b76a143dd_5.png)

![](img/51210a0f0125f38c1e3b5d8b76a143dd_6.png)

**给初学者的建议**：你可以从下载提供的代码开始，尝试修改参数（如`RSI_length`, `confirmation_bars`, `atr_multiplier_sl`等），在不同品种和时段进行测试，观察策略表现的变化。这是理解量化策略如何运作的最佳途径。记住，不存在永远有效的“圣杯”策略，持续的研究和风控才是关键。