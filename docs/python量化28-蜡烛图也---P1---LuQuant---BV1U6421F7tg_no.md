# Python量化28：1：Heikin-Ashi蜡烛图简介与构建 🕯️

在本节课中，我们将学习如何在Python中构建Heikin-Ashi（平均足）蜡烛图，并探索两种基于此图表的简单交易策略。Heikin-Ashi蜡烛图通过对普通K线的价格进行平滑处理，能更清晰地展示市场趋势。

## 概述

Heikin-Ashi蜡烛图并非市场的真实价格，而是通过特定公式计算得出的平滑价格。它由四个关键价格构成：开盘价、最高价、最低价和收盘价。其计算公式如下：

*   **Heikin-Ashi收盘价** = （普通K线的开盘价 + 最高价 + 最低价 + 收盘价） / 4
*   **Heikin-Ashi开盘价** = （前一根Heikin-Ashi蜡烛的开盘价 + 前一根Heikin-Ashi蜡烛的收盘价） / 2
*   **Heikin-Ashi最高价** = max（普通K线的最高价， Heikin-Ashi开盘价， Heikin-Ashi收盘价）
*   **Heikin-Ashi最低价** = min（普通K线的最低价， Heikin-Ashi开盘价， Heikin-Ashi收盘价）

这种计算方式使得图表看起来更平滑，减少了市场噪音，一些交易者认为它比普通蜡烛图更能有效识别趋势。

---

# Python量化28：2：基于Heikin-Ashi的策略原理 📈

上一节我们介绍了Heikin-Ashi蜡烛图的构成。本节中，我们来看看如何利用它来构建交易策略。我们将探讨两种策略变体，它们都基于移动平均线来判断趋势方向。

## 策略一：移动平均线交叉触发

该策略使用两条移动平均线（一条快速，一条慢速）来确定市场趋势。
*   当快速移动平均线 **高于** 慢速移动平均线时，判定为**上升趋势**，我们寻找**买入**机会。
*   当快速移动平均线 **低于** 慢速移动平均线时，判定为**下降趋势**，我们寻找**卖出**机会。

![](img/17efe053a0890aea45300f9584bd590b_1.png)

具体的入场触发条件是：在明确的趋势中，Heikin-Ashi蜡烛的收盘价在趋势方向上穿越快速移动平均线。
*   **买入信号**：处于上升趋势，且一根Heikin-Ashi蜡烛从**下方**收盘**高于**快速移动平均线。
*   **卖出信号**：处于下降趋势，且一根Heikin-Ashi蜡烛从**上方**收盘**低于**快速移动平均线。

## 策略二：添加十字星与短尾蜡烛过滤

这是第一个策略的变体，通过添加额外的蜡烛形态条件来提高信号质量。在趋势方向确认后，我们寻找特定的K线组合。
*   在**下降趋势**中，寻找一根“十字星”蜡烛，后面紧跟一根“短尾”蜡烛（即实体较小，几乎没有上下影线），且第二根蜡烛开盘高于快速移动平均线但收盘低于它。这被视为动能减弱的迹象，可触发卖出信号。
*   在**上升趋势**中，则寻找相反的组合来触发买入信号。

寻找“无尾”或短尾蜡烛的原因是，它可能表明当前趋势的动能正在减弱，这取决于我们观察的趋势方向。

---

# Python量化28：3：在Python中构建Heikin-Ashi图表 💻

理解了策略原理后，我们现在动手在Python中实现Heikin-Ashi蜡烛图的计算。我们将使用Pandas库来处理数据。

首先，导入必要的库并加载股票价格数据。

```python
import pandas as pd
import numpy as np
import yfinance as yf
import matplotlib.pyplot as plt

# 下载Russell 1000指数数据（示例）
data = yf.download('^RUI', start='2012-01-01', end='2022-12-31')
# 删除开盘价等于收盘价的日期（如节假日）
data = data[data['Open'] != data['Close']].reset_index(drop=True)
print(data.head(10))
```

接下来，我们根据公式计算Heikin-Ashi的四个价格。需要注意的是，Heikin-Ashi开盘价的计算依赖于前一根Heikin-Ashi蜡烛，因此我们需要按顺序循环计算。

![](img/17efe053a0890aea45300f9584bd590b_3.png)

```python
# 初始化Heikin-Ashi列为NaN
data['HA_Close'] = (data['Open'] + data['High'] + data['Low'] + data['Close']) / 4
data['HA_Open'] = 0.0
data['HA_High'] = 0.0
data['HA_Low'] = 0.0

![](img/17efe053a0890aea45300f9584bd590b_5.png)

# 第一根HA蜡烛的开盘价用普通K线的开盘价近似
data.loc[0, 'HA_Open'] = data.loc[0, 'Open']

![](img/17efe053a0890aea45300f9584bd590b_7.png)

# 循环计算后续的HA蜡烛
for i in range(1, len(data)):
    # HA开盘价 = (前一根HA开盘价 + 前一根HA收盘价) / 2
    data.loc[i, 'HA_Open'] = (data.loc[i-1, 'HA_Open'] + data.loc[i-1, 'HA_Close']) / 2
    # HA最高价 = max(普通最高价， HA开盘价， HA收盘价)
    data.loc[i, 'HA_High'] = max(data.loc[i, 'High'], data.loc[i, 'HA_Open'], data.loc[i, 'HA_Close'])
    # HA最低价 = min(普通最低价， HA开盘价， HA收盘价)
    data.loc[i, 'HA_Low'] = min(data.loc[i, 'Low'], data.loc[i, 'HA_Open'], data.loc[i, 'HA_Close'])

# 添加移动平均线等指标
data['MA20'] = data['HA_Close'].rolling(window=20).mean()
data['MA50'] = data['HA_Close'].rolling(window=50).mean()
```

现在，我们可以绘制Heikin-Ashi图表进行可视化验证。你会注意到，Heikin-Ashi蜡烛的开盘价通常位于前一根蜡烛实体的中部，这使得图表呈现出独特的平滑外观。

```python
# 绘制Heikin-Ashi蜡烛图（此处为简化示意图，实际需使用mplfinance等库绘制标准蜡烛图）
fig, ax = plt.subplots(figsize=(12,6))
ax.plot(data.index, data['HA_Close'], label='Heikin-Ashi Close')
ax.plot(data.index, data['MA20'], label='MA20', color='red', alpha=0.7)
ax.plot(data.index, data['MA50'], label='MA50', color='blue', alpha=0.7)
ax.set_title('Heikin-Ashi Chart with Moving Averages')
ax.legend()
plt.show()
```

---

![](img/17efe053a0890aea45300f9584bd590b_9.png)

# Python量化28：4：策略信号生成与可视化 🎯

图表构建完成后，本节我们来实现策略一的信号生成逻辑，并将买卖信号可视化在图表上。

以下是生成交易信号的函数。它遍历数据框的每一行，根据移动平均线的位置关系和Heikin-Ashi蜡烛的穿越行为来标记信号。

```python
def generate_signals(df):
    """
    根据策略一生成交易信号。
    信号: 2 = 买入, 1 = 卖出, 0 = 无信号
    """
    signals = pd.Series(0, index=df.index) # 初始化信号列为0

    for i in range(1, len(df)):
        # 条件：上升趋势 (MA20 > MA50) 且 HA蜡烛上穿MA20
        if df.loc[i, 'MA20'] > df.loc[i, 'MA50'] and \
           df.loc[i-1, 'HA_Close'] <= df.loc[i-1, 'MA20'] and \
           df.loc[i, 'HA_Close'] > df.loc[i, 'MA20']:
            signals.iloc[i] = 2 # 买入信号
        # 条件：下降趋势 (MA20 < MA50) 且 HA蜡烛下穿MA20
        elif df.loc[i, 'MA20'] < df.loc[i, 'MA50'] and \
             df.loc[i-1, 'HA_Close'] >= df.loc[i-1, 'MA20'] and \
             df.loc[i, 'HA_Close'] < df.loc[i, 'MA20']:
            signals.iloc[i] = 1 # 卖出信号
    return signals

# 应用函数生成信号
data['Signal'] = generate_signals(data)
```

![](img/17efe053a0890aea45300f9584bd590b_11.png)

为了更直观地评估信号，我们在图表上标记出买卖点。

![](img/17efe053a0890aea45300f9584bd590b_12.png)

![](img/17efe053a0890aea45300f9584bd590b_13.png)

```python
# 在图表上标记信号点
buy_signals = data[data['Signal'] == 2]
sell_signals = data[data['Signal'] == 1]

fig, ax = plt.subplots(figsize=(14,7))
# 绘制HA收盘价和均线
ax.plot(data.index, data['HA_Close'], label='Heikin-Ashi Close', alpha=0.5)
ax.plot(data.index, data['MA20'], label='MA20', color='red', alpha=0.7)
ax.plot(data.index, data['MA50'], label='MA50', color='blue', alpha=0.7)
# 标记买卖点
ax.scatter(buy_signals.index, buy_signals['HA_Close'], marker='^', color='green', s=100, label='Buy Signal', zorder=5)
ax.scatter(sell_signals.index, sell_signals['HA_Close'], marker='v', color='red', s=100, label='Sell Signal', zorder=5)
ax.set_title('Trading Signals on Heikin-Ashi Chart')
ax.legend()
plt.show()
```

**关键注意事项**：信号是基于Heikin-Ashi图表生成的，但实际交易和回测必须使用**原始的真实价格数据**（普通K线）。因为Heikin-Ashi价格是平滑值，并非市场真实成交价。这是一个常见但重要的区别。

![](img/17efe053a0890aea45300f9584bd590b_15.png)

---

![](img/17efe053a0890aea45300f9584bd590b_17.png)

# Python量化28：5：回测实现与策略评估 ⚖️

![](img/17efe053a0890aea45300f9584bd590b_19.png)

信号已经生成，本节我们将实现一个简单的回测框架来评估策略表现。这里会涉及止损、止盈和仓位管理的基本逻辑。

![](img/17efe053a0890aea45300f9584bd590b_20.png)

首先，我们定义一些回测参数和交易规则。例如，使用固定比例止损，并在出现反向Heikin-Ashi蜡烛时平仓。

```python
# 回测参数
initial_capital = 10000
position = 0 # 当前持仓， 1为多头， -1为空头， 0为无持仓
cash = initial_capital
portfolio_value = []
stop_loss_pct = 0.02 # 2% 止损
take_profit_ratio = 1.5 # 止盈/止损比例

# 用于记录交易
trades = []
```

![](img/17efe053a0890aea45300f9584bd590b_22.png)

![](img/17efe053a0890aea45300f9584bd590b_23.png)

以下是简化的回测循环核心逻辑。它遍历每个交易日，检查是否有新信号触发开仓，或是否达到平仓条件（止损、止盈或反向信号）。

```python
for i in range(1, len(data)):
    current_price = data.loc[i, 'Close'] # 使用真实收盘价进行交易
    ha_signal = data.loc[i, 'Signal']

    # 平仓逻辑检查
    if position != 0:
        # 计算盈亏比例 (简化)
        if position == 1: # 多头
            # 检查止损止盈或出现红色HA蜡烛（可能动能减弱）
            # ... (具体逻辑需根据进场价计算)
            pass
        elif position == -1: # 空头
            # 检查止损止盈或出现绿色HA蜡烛
            # ... (具体逻辑需根据进场价计算)
            pass

    # 开仓逻辑
    if position == 0:
        if ha_signal == 2: # 买入信号
            # 计算开仓数量、设置止损止盈价
            # position = 1
            # 记录交易
            pass
        elif ha_signal == 1: # 卖出信号
            # 计算开仓数量、设置止损止盈价 (假设可以做空)
            # position = -1
            # 记录交易
            pass

    # 更新每日投资组合价值
    portfolio_value.append(cash + position * current_price)
```

![](img/17efe053a0890aea45300f9584bd590b_25.png)

回测完成后，我们可以计算总收益率、夏普比率等指标，并绘制资金曲线。

```python
# 计算总回报
final_portfolio_value = portfolio_value[-1]
total_return = (final_portfolio_value - initial_capital) / initial_capital * 100
print(f"初始资金: ${initial_capital}")
print(f"最终资金: ${final_portfolio_value:.2f}")
print(f"总收益率: {total_return:.2f}%")

# 绘制资金曲线
plt.figure(figsize=(10,5))
plt.plot(portfolio_value)
plt.title('Portfolio Value Over Time')
plt.xlabel('Day')
plt.ylabel('Portfolio Value ($)')
plt.grid(True)
plt.show()
```

**重要提示**：本例中的回测非常简化，未考虑交易费用、滑点、仓位大小动态调整等现实因素。这里的止盈止损逻辑（如基于前两根蜡烛低点）也需要你根据自身风险偏好进行设计和优化。移动平均线的参数（20和50）也未经优化，不同的市场和时期可能需要调整。

---

# Python量化28：6：总结与思考 🤔

本节课中，我们一起学习了Heikin-Ashi蜡烛图的原理、构建方法，以及如何基于它设计简单的移动平均线交叉策略。

我们首先在Python中成功计算并绘制了Heikin-Ashi图表，看到了其平滑趋势的特点。然后，我们实现了两种策略的信号生成逻辑，并将买卖点可视化。最后，我们构建了一个基础的回测框架来评估策略的历史表现。

![](img/17efe053a0890aea45300f9584bd590b_27.png)

核心要点总结：
1.  **Heikin-Ashi是分析工具**：它主要用于生成更清晰的趋势信号，但**实际交易必须使用原始市场价格**。
2.  **策略需要完善**：本节课展示的策略是基础原型。在实际应用中，必须考虑：
    *   **参数优化**：移动平均线的周期、止损止盈比例。
    *   **风险管理**：更精细的仓位管理和止损策略。
    *   **市场适应性**：策略在不同市场环境（趋势、震荡）下的表现。
    *   **综合过滤**：可以像策略二一样，结合RSI等其他指标过滤假信号。
3.  **回测的局限性**：简单的回测无法模拟所有市场微观结构。回测结果仅供参考，不代表未来表现。

![](img/17efe053a0890aea45300f9584bd590b_29.png)

你可以将此代码作为起点，尝试修改移动平均线周期、添加其他技术指标（如RSI），或者设计更复杂的入场/出场规则，从而开发属于自己的交易策略。记住，持续的学习、测试和风险管理才是量化交易的核心。