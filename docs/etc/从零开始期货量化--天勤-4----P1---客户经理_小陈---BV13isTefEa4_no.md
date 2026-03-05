# 从零开始期货量化--天勤：4.6：如何计算资金曲线 📈

![](img/594c0a220ef1436db19e367ebb7cb6c7_1.png)

在本节课中，我们将学习如何在回测框架中计算资金曲线，并对计算出的资金曲线进行策略评价。这是量化策略开发中至关重要的一步，它直接反映了策略的盈利能力与风险水平。

## 概述

上节课我们介绍了如何根据交易信号计算持仓信息。本节中，我们来看看如何基于持仓信息，计算策略的净值表现，即资金曲线。核心内容包括两个部分：资金曲线的计算函数与策略评价指标。

## 资金曲线计算原理

如果按照最原始的方法，即每次开仓都用当前总资产计算手数，平仓后更新总资产，再进入下一个周期，计算会非常复杂且难以编程实现。

因此，我们采用一种更巧妙的方法：**将每个独立的交易周期视为一个独立的投资单元**。

以下是计算原理的步骤分解：

1.  **独立周期**：将每次开仓到下次开仓（或平仓）之间的时间段视为一个独立的交易周期。
2.  **归一化起点**：在每个周期开始时，将初始净值设为 **1**。
3.  **计算周期收益率**：计算该周期结束时的净值。例如，周期结束时净值为 **1.1**，则表示该周期收益率为 **10%**。
4.  **复利拼接**：将所有交易周期的收益率（加1后）连乘，即可得到最终的累计净值曲线。
    *   公式：`最终净值 = (1 + 收益率1) * (1 + 收益率2) * ... * (1 + 收益率N)`
    *   例如：第一个周期收益10%（净值1.1），第二个周期收益10%（净值1.1），则最终净值为 `1.1 * 1.1 = 1.21`。
5.  **处理亏损**：若某周期亏损10%（净值0.9），则参与连乘即可，例如 `1.1 * 0.9 = 0.99`。

![](img/594c0a220ef1436db19e367ebb7cb6c7_3.png)

这种方法完美解决了多周期、带风控的复杂交易情况，并使代码实现变得清晰简洁。

## 代码实现详解

核心计算在 `evaluate.py` 脚本的 `equity_curve_type1` 或 `equity_curve_type2` 函数中。两者逻辑相同，主要为适应不同成交模式（如开盘价成交、均价成交）而设计。

为了便于理解，我们结合代码与输出数据，分步解析资金曲线的计算过程。首先，我们需要在策略参数中设置相关字段。

![](img/594c0a220ef1436db19e367ebb7cb6c7_5.png)

以下是计算所需的字段及其含义：
*   `initial_capital`: 初始资金，如 1,000,000。
*   `invest_ratio`: 投资比例，决定每次开仓投入初始资金的比例。
*   `slippage`: 滑点，模拟实际交易中的冲击成本。
*   `commission_ratio`: 交易手续费率。
*   `margin_ratio`: 保证金比例。
*   `min_margin_ratio`: 最低维持保证金比例，用于判断爆仓。
*   `value_per_tick`: 每跳价值，即合约价格变动一个最小单位对应的盈亏。

### 步骤一：确定交易周期

首先，我们需要根据持仓 `position` 的变化，识别出每一个开仓和平仓点，并对每个交易周期进行分组。

```python
# 识别开仓日：当日持仓不为零，且与前一日持仓不同
df['open_flag'] = (df['position'] != 0) & (df['position'] != df['position'].shift(1))
# 识别平仓日：当日持仓不为零，且与后一日持仓不同（或后一日为零）
df['close_flag'] = (df['position'] != 0) & (df['position'] != df['position'].shift(-1))

# 为每个交易周期分配一个唯一的开始时间标识
df.loc[df['open_flag'], 'start_time'] = df['datetime']
df['start_time'] = df['start_time'].fillna(method='ffill')
df.loc[df['position']==0, 'start_time'] = pd.NaT
```

### 步骤二：计算开/平仓价格与手数

确定周期后，计算实际的开仓价、平仓价和交易手数。

```python
# 计算实际开仓价（考虑滑点）
df['open_price_actual'] = df['open_price'] + df['slippage'] * df['position']
# 计算投入资金
invest_capital = initial_capital * invest_ratio
# 计算开仓手数（向下取整，保证为整数）
df['contract_num'] = np.floor(invest_capital / (df['open_price_actual'] * value_per_tick))
# 计算实际平仓价（考虑滑点）
df['close_price_actual'] = df['close_price'] - df['slippage'] * df['position']
```

### 步骤三：计算动态权益与爆仓

在持仓周期内，每日净值都会随收盘价波动。我们需要计算每日的动态权益、利润，并严格检查是否触发爆仓条件。

```python
# 计算每日浮动盈亏（基于收盘价）
df['daily_profit'] = (df['close'] - df['open_price_actual']) * df['position'] * df['contract_num'] * value_per_tick
# 在平仓日，盈亏需按实际平仓价重新计算
df.loc[df['close_flag'], 'daily_profit'] = (df['close_price_actual'] - df['open_price_actual']) * df['position'] * df['contract_num'] * value_per_tick

# 计算每日净值
df['net_value'] = invest_capital + df['daily_profit'].cumsum() - df['commission'] # 佣金需另行计算并扣除

# 计算当日净值可能达到的最低点（用于爆仓检查）
# 做多时，取当日最低价；做空时，取当日最高价
df['price_min'] = np.where(df['position']==1, df['low'], df['high'])
df['min_equity'] = invest_capital + (df['price_min'] - df['open_price_actual']) * df['position'] * df['contract_num'] * value_per_tick

# 计算保证金比率
df['margin_level'] = df['min_equity'] / (abs(df['position'] * df['contract_num'] * df['open_price_actual'] * value_per_tick * margin_ratio))
# 判断是否爆仓：保证金比率低于最低要求
df['is_liquidated'] = df['margin_level'] < min_margin_ratio
# 如果爆仓，后续净值归零
df.loc[df['is_liquidated'].cummax(), 'net_value'] = 0
```

### 步骤四：计算资金曲线

最后，基于每日净值计算资金曲线的日收益率，并通过复利方式拼接成最终曲线。

```python
# 计算日收益率
df['equity_change'] = df['net_value'].pct_change()
# 处理首日收益率
df.loc[df.index[0], 'equity_change'] = df.loc[df.index[0], 'net_value'] / invest_capital - 1
# 将空值（如空仓期）的收益率设为0
df['equity_change'].fillna(0, inplace=True)

# 通过复利计算累计资金曲线 (从1开始)
df['equity_curve'] = (1 + df['equity_change']).cumprod()
```

## 策略评价指标计算

计算出资金曲线后，我们需要用一些量化指标来评价策略的表现。以下是核心评价指标的计算方法：

1.  **累计净值**：资金曲线的最后一个值。
    *   `累计净值 = equity_curve.iloc[-1]`
2.  **年化收益率**：将总收益率折算成年收益率。
    *   `年化收益率 = (累计净值 ** (365 / 交易天数) - 1)`
3.  **历史最高净值**：资金曲线运行过程中的最高点。
    *   `历史最高净值 = equity_curve.cummax()`
4.  **最大回撤**：从历史最高点下跌的最大幅度。
    *   `回撤 = (equity_curve - 历史最高净值) / 历史最高净值`
    *   `最大回撤 = 回撤.min()`
5.  **最大回撤期**：最大回撤开始和结束的日期。
    *   通过定位最大回撤结束日，向前寻找最近的最高点日期来确定开始日。

将这些指标计算并整理后，即可输出一份清晰的策略评价报告。

## 可视化资金曲线

使用 `matplotlib` 库可以轻松地将资金曲线绘制出来，直观展示策略的增长过程与回撤情况。

```python
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei'] # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus'] = False # 用来正常显示负号

plt.figure(figsize=(12,6))
plt.plot(df['datetime'], df['equity_curve'], label='策略净值曲线')
plt.title('策略资金曲线')
plt.xlabel('日期')
plt.ylabel('净值')
plt.legend()
plt.grid(True)
plt.show()
```

## 总结

本节课中，我们一起学习了量化回测的核心环节——资金曲线的计算与评价。我们从原理出发，讲解了如何通过**周期归一化与复利拼接**的方法高效计算净值；随后，深入代码细节，逐步拆解了开平仓处理、动态权益计算、爆仓检查以及最终曲线生成的完整流程；最后，我们介绍了常用的策略评价指标及其计算方法，并完成了资金曲线的可视化。

![](img/594c0a220ef1436db19e367ebb7cb6c7_7.png)

![](img/594c0a220ef1436db19e367ebb7cb6c7_9.png)

理解并掌握这套资金曲线计算框架，是检验策略在实盘中能否盈利的基石。佟掌柜提供的这套框架已非常贴近实盘，各位老板可以在其基础上，根据更复杂的策略需求进行微调和深化。只有深刻理解数据与程序间的每个逻辑，才能在策略研发的道路上得心应手。量化是工具，策略才是关键，希望本课能助你在量化交易之路上更进一步。