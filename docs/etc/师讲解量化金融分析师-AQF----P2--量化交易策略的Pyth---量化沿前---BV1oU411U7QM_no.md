# 量化金融专业知识与实务：P2-02：CCI策略的Python实现与回测 🐍📈

在本节课中，我们将学习如何用Python实现CCI（商品通道指数）交易策略并进行回测。我们将重点介绍两种代码编写方法：向量填充法和循环遍历法，并详细讲解如何使用`ta-lib`库计算技术指标。

## 数据准备与CCI计算

![](img/cb4360174eecb01a8297ef52ea9aa741_1.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_3.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_5.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_7.png)

首先，我们需要导入必要的库并获取股票数据。我们将使用`tushare`获取数据，并使用`ta-lib`库高效地计算CCI指标。

![](img/cb4360174eecb01a8297ef52ea9aa741_9.png)

```python
import pandas as pd
import numpy as np
import tushare as ts
import talib as ta
import matplotlib.pyplot as plt

![](img/cb4360174eecb01a8297ef52ea9aa741_11.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_12.png)

# 设置中文字体显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

![](img/cb4360174eecb01a8297ef52ea9aa741_14.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_16.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_18.png)

# 获取股票数据（以中信证券600030为例）
stock = ts.get_k_data('600030', start='2017-01-01', end='2018-01-01')
stock.index = pd.to_datetime(stock.date)
stock = stock.sort_index()

# 使用ta-lib计算CCI指标（周期为20天）
stock['CCI'] = ta.CCI(np.array(stock.high), np.array(stock.low), np.array(stock.close), timeperiod=20)
```

**核心概念解释**：`ta.CCI()`函数需要四个参数：最高价序列、最低价序列、收盘价序列和计算周期。`np.array()`用于将Pandas Series转换为NumPy数组以满足函数输入要求。

## 策略逻辑与信号生成

上一节我们准备好了数据并计算了CCI指标。本节中，我们来看看如何根据CCI值生成交易信号。策略逻辑是：当CCI从低于-100向上突破-100时，产生买入信号（+1）；当CCI从高于+100向下跌破+100时，产生卖出信号（-1）。

以下是生成交易信号的步骤：

1.  **计算滞后值**：我们需要前一日和前两日的CCI值来判断“突破”行为。
    ```python
    stock['yesterday_CCI'] = stock['CCI'].shift(1)
    stock['day_before_yesterday_CCI'] = stock['CCI'].shift(2)
    ```

2.  **生成初始交易信号**：根据策略逻辑，在突破点标记信号，其余位置设为空值（`np.nan`）。
    ```python
    # 方法一：使用np.where和逻辑与(&)
    buy_condition = (stock['day_before_yesterday_CCI'] < -100) & (stock['yesterday_CCI'] > -100)
    sell_condition = (stock['day_before_yesterday_CCI'] > 100) & (stock['yesterday_CCI'] < 100)

    stock['signal'] = np.where(buy_condition, 1, np.nan)
    stock['signal'] = np.where(sell_condition, -1, stock['signal'])
    ```

## 持仓计算与收益回测

在得到了离散的交易信号点后，我们需要将其转化为连续的持仓序列。这意味着在下一个反向信号出现之前，应维持当前的持仓状态。

以下是计算持仓和策略收益的过程：

1.  **计算持仓**：使用`fillna`方法进行前向填充，将离散的信号点扩展为连续的持仓序列。初始空值部分（即策略开始前）填充为0，代表空仓。
    ```python
    stock['position'] = stock['signal'].fillna(method='ffill').fillna(0)
    ```

2.  **计算收益**：
    *   计算股票的每日收益率。
    *   **关键点**：由于我们的信号是基于`昨天`和`前天`的数据生成的，因此在计算当日策略收益时，可以直接用当日的持仓乘以当日的股票收益率，**无需再进行`shift(1)`操作**，这避免了未来函数问题。
    ```python
    stock['return'] = stock['close'].pct_change()
    stock['strategy_return'] = stock['position'] * stock['return']

    # 计算累计收益
    stock['cum_stock_return'] = (1 + stock['return']).cumprod()
    stock['cum_strategy_return'] = (1 + stock['strategy_return']).cumprod()
    ```

## 结果可视化

最后，我们将结果可视化，直观对比策略收益与股票本身收益。

```python
# 绘制CCI指标与交易信号
fig, axes = plt.subplots(3, 1, figsize=(12, 10), sharex=True)
axes[0].plot(stock['close'], label='收盘价')
axes[0].set_title('600030 收盘价')
axes[0].legend()

axes[1].plot(stock['CCI'], label='CCI', color='orange')
axes[1].axhline(y=100, color='r', linestyle='--', alpha=0.5)
axes[1].axhline(y=-100, color='g', linestyle='--', alpha=0.5)
axes[1].set_title('CCI指标')
axes[1].legend()

![](img/cb4360174eecb01a8297ef52ea9aa741_20.png)

![](img/cb4360174eecb01a8297ef52ea9aa741_22.png)

axes[2].plot(stock['position'], label='持仓信号', drawstyle='steps-post')
axes[2].set_title('交易持仓信号')
axes[2].legend()

plt.tight_layout()
plt.show()

# 绘制累计收益对比图
plt.figure(figsize=(10, 6))
plt.plot(stock['cum_stock_return'], label='股票买入持有收益')
plt.plot(stock['cum_strategy_return'], label='CCI策略收益')
plt.title('CCI策略收益对比')
plt.legend()
plt.show()
```

## 代码结构优化：信号与持仓分离

为了使逻辑更清晰，我们可以将交易信号生成和持仓计算分为两步。这种方法代码更长，但结构更明了。

```python
# 第一步：单独生成交易信号（仅在有突破的日期有值，其余为0）
stock['trade_signal'] = 0
stock['trade_signal'] = np.where(buy_condition, 1, stock['trade_signal'])
stock['trade_signal'] = np.where(sell_condition, -1, stock['trade_signal'])

# 第二步：基于交易信号计算连续持仓
# 将非零信号点（实际交易点）保留，零值（无交易日）设为空值，然后前向填充
stock['position_separate'] = stock['trade_signal'].replace(0, np.nan).fillna(method='ffill').fillna(0)
```
这种方法最终得到的`position_separate`列与之前直接得到的`position`列结果完全一致。

## 总结

![](img/cb4360174eecb01a8297ef52ea9aa741_24.png)

本节课中我们一起学习了CCI交易策略的完整Python实现流程。我们掌握了使用`ta-lib`库计算技术指标的方法，理解了如何将“突破”逻辑转化为具体的交易信号，并学会了通过前向填充将离散信号转化为连续持仓。同时，我们重点区分了交易信号与持仓的概念，并了解了两种代码组织方式。最后，我们完成了策略的回测与可视化分析，为评估策略效果奠定了基础。下一节，我们将使用循环遍历法来实现更复杂的布林带策略。