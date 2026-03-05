# 量化金融分析师.AQF：03：量化投资与技术分析_3布林带策略_1

![](img/e86972cd3069dd0ea0f8ee38223975b3_1.png)

## 概述
在本节课中，我们将学习布林带交易策略的Python实现方法。我们将重点介绍一种与之前不同的策略编写思路——循环法，并详细讲解如何使用`iterrows()`方法逐行处理数据以计算持仓信号。通过本节课，你将掌握实现复杂交易逻辑的基础方法。

## 数据准备
首先，我们需要导入必要的库并获取股票数据。我们将使用`tushare`获取沪深300指数从2016年1月1日到2017年7月1日的数据。

![](img/e86972cd3069dd0ea0f8ee38223975b3_3.png)

```python
import pandas as pd
import numpy as np
import tushare as ts
import talib
import matplotlib.pyplot as plt

![](img/e86972cd3069dd0ea0f8ee38223975b3_5.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_7.png)

# 获取数据
df = ts.get_k_data('hs300', start='2016-01-01', end='2017-07-01')
df.index = pd.to_datetime(df.date)
df = df.sort_index()
```

## 计算布林带指标
布林带由三条线组成：中轨（`middle_band`）、上轨（`upper_band`）和下轨（`lower_band`）。我们将使用`talib`库的`BBANDS`函数进行计算。

![](img/e86972cd3069dd0ea0f8ee38223975b3_9.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_11.png)

```python
# 计算布林带
close_prices = np.array(df.close)
upper_band, middle_band, lower_band = talib.BBANDS(close_prices,
                                                    timeperiod=20,
                                                    nbdevup=2,
                                                    nbdevdn=2,
                                                    matype=0)

# 将结果存入DataFrame
df['upper'] = upper_band
df['middle'] = middle_band
df['lower'] = lower_band
```

## 绘制布林带
为了直观地观察布林带与股价的关系，我们可以将数据可视化。

```python
# 绘制布林带
plt.figure(figsize=(12, 8))
plt.plot(df.close, label='Close Price', alpha=0.5)
plt.plot(df.upper, label='Upper Band', linestyle='--')
plt.plot(df.middle, label='Middle Band', linestyle='--')
plt.plot(df.lower, label='Lower Band', linestyle='--')
plt.title('Bollinger Bands')
plt.legend()
plt.show()
```

![](img/e86972cd3069dd0ea0f8ee38223975b3_13.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_15.png)

## 交易信号和持仓信号的计算
上一节我们计算并绘制了布林带，本节中我们来看看如何根据策略逻辑生成交易信号。本策略的核心逻辑是：
*   **做多信号**：股价先跌破下轨，随后又回升至下轨之上。
*   **做空信号**：股价先突破上轨，随后又回落至上轨之下。

### 计算交易信号
首先，我们需要计算每日的收盘价与前一日、前两日的布林带上下轨的关系，以判断是否出现交易信号。

```python
# 计算前一日和前两日的数据
df['yesterday_close'] = df.close.shift(1)
df['yesterday_upper'] = df.upper.shift(1)
df['yesterday_lower'] = df.lower.shift(1)

![](img/e86972cd3069dd0ea0f8ee38223975b3_17.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_19.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_21.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_23.png)

df['day_before_close'] = df.close.shift(2)
df['day_before_upper'] = df.upper.shift(2)
df['day_before_lower'] = df.lower.shift(2)

# 初始化信号列
df['signal'] = 0

# 生成做多信号 (1)
long_condition = (df.day_before_close < df.day_before_lower) & (df.yesterday_close > df.yesterday_lower)
df.loc[long_condition, 'signal'] = 1

# 生成做空信号 (-1)
short_condition = (df.day_before_close > df.day_before_upper) & (df.yesterday_close < df.yesterday_upper)
df.loc[short_condition, 'signal'] = -1
```

![](img/e86972cd3069dd0ea0f8ee38223975b3_25.png)

### 理解 iterrows() 方法
在计算持仓信号前，我们需要理解`iterrows()`方法。与之前向量化的前向填充方法不同，循环法需要逐行处理数据。`iterrows()`允许我们遍历DataFrame的每一行。

以下是`iterrows()`的基本用法示例：

![](img/e86972cd3069dd0ea0f8ee38223975b3_27.png)

```python
# 创建一个示例DataFrame
sample_df = pd.DataFrame(np.random.randn(4, 2), columns=['A', 'B'], index=['a', 'b', 'c', 'd'])

# 使用 iterrows() 遍历
for index, row in sample_df.iterrows():
    print(f"行索引: {index}")
    print(f"该行数据（Series格式）:\n{row}")
    print(f"访问该行‘B’列的值: {row['B']}")
    print("-" * 20)
```
在循环中，`index`是行的标签（如日期），`row`是一个`Series`对象，其索引是列名，值是该行对应列的数据。

### 计算持仓信号
现在，我们使用循环法，基于交易信号`signal`来计算实际的持仓`position`。规则是：持仓状态会持续，直到出现相反的交易信号才会改变。

```python
# 初始化持仓列
df['position'] = 0
# 初始化一个变量来跟踪当前持仓状态 (0:空仓, 1:多仓, -1:空仓)
current_position = 0

# 使用 iterrows() 逐行循环
for date, row in df.iterrows():
    # 获取当前行的交易信号
    current_signal = row['signal']

    # 根据信号和当前持仓，决定新的持仓
    if current_signal == 1:
        current_position = 1  # 出现做多信号，持多仓
    elif current_signal == -1:
        current_position = -1 # 出现做空信号，持空仓
    # 如果 signal 为 0，则保持原有持仓不变

    # 将计算出的持仓状态赋值给当前行的‘position’列
    df.loc[date, 'position'] = current_position
```

## 策略收益及可视化
最后，我们可以计算策略的简单收益，并将股价、交易信号和持仓情况绘制在一起进行观察。

![](img/e86972cd3069dd0ea0f8ee38223975b3_29.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_31.png)

```python
# 计算策略每日收益率（假设在信号出现时以收盘价交易）
df['returns'] = df.close.pct_change()
df['strategy_returns'] = df['position'].shift(1) * df['returns']

# 绘制结果
fig, axes = plt.subplots(3, 1, figsize=(14, 10), sharex=True)

# 子图1: 价格与布林带
axes[0].plot(df.close, label='Close Price', linewidth=1)
axes[0].plot(df.upper, label='Upper Band', linestyle='--', linewidth=0.7)
axes[0].plot(df.lower, label='Lower Band', linestyle='--', linewidth=0.7)
axes[0].set_title('Price and Bollinger Bands')
axes[0].legend()
axes[0].grid(True)

# 子图2: 交易信号
axes[1].plot(df.index, df.signal, drawstyle='steps-post', label='Trading Signal', color='orange')
axes[1].set_ylabel('Signal')
axes[1].set_title('Trading Signals (1: Long, -1: Short)')
axes[1].legend()
axes[1].grid(True)
axes[1].set_yticks([-1, 0, 1])

![](img/e86972cd3069dd0ea0f8ee38223975b3_33.png)

# 子图3: 持仓情况
axes[2].plot(df.index, df.position, drawstyle='steps-post', label='Position', color='green')
axes[2].set_ylabel('Position')
axes[2].set_xlabel('Date')
axes[2].set_title('Holding Position (1: Long, -1: Short, 0: Flat)')
axes[2].legend()
axes[2].grid(True)
axes[2].set_yticks([-1, 0, 1])

plt.tight_layout()
plt.show()
```

![](img/e86972cd3069dd0ea0f8ee38223975b3_35.png)

![](img/e86972cd3069dd0ea0f8ee38223975b3_37.png)

## 总结
本节课中我们一起学习了布林带交易策略的完整实现流程。我们首先计算了布林带指标并将其可视化，然后定义了策略逻辑并生成了交易信号。最关键的是，我们引入了`iterrows()`循环法来计算持仓信号，这种方法为后续实现更复杂的交易逻辑（如涉及日内数据或复杂状态判断的策略）打下了基础。虽然循环法在速度上可能不如向量化方法，但其逻辑清晰，灵活性高，是量化策略开发中必须掌握的核心技能之一。