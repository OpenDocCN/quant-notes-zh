# 人工智能数据挖掘实战：P14：策略收益效果分析 📈

![](img/b02fb2fae5a9a48db2455d3730732dd3_1.png)

在本节课中，我们将学习如何计算并分析一个交易策略的最终收益。我们将通过一个具体的例子，对比“什么都不做”的原始股价收益与我们设计的“双均线策略”收益，从而量化策略的有效性。

## 计算原始股价增长率

![](img/b02fb2fae5a9a48db2455d3730732dd3_3.png)

上一节我们介绍了如何通过均线交叉生成交易信号（`position`）。本节中，我们来看看如何基于这些信号计算策略的最终收益。首先，我们需要计算原始股价的每日增长率。

以下是计算每日股价增长率的步骤：
1.  获取股价数据。
2.  计算每日的报酬率（增长率），公式为：`当日股价 / 前一日股价`。
3.  在代码中，我们使用 `shift(1)` 方法来获取前一日的数据。

对应的核心代码如下：
```python
# 假设 df[‘close’] 是股价序列
returns = df[‘close’] / df[‘close’].shift(1)
```
执行这段代码后，我们得到了一个表示每日增长率的序列 `returns`。

## 计算策略收益

![](img/b02fb2fae5a9a48db2455d3730732dd3_5.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_6.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_7.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_8.png)

现在，我们已经有了原始股价的增长率。接下来，我们需要将交易信号应用到这些增长率上，以计算策略的收益。

![](img/b02fb2fae5a9a48db2455d3730732dd3_9.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_10.png)

我们的策略逻辑是：当 `position` 信号为 1 时，我们跟随市场走势（即使用原始增长率）；当 `position` 信号为 -1 时，我们进行反向操作（即使用增长率的倒数，相当于做空或反向交易）。因此，策略的每日收益可以通过将 `position` 信号与原始 `returns` 相乘得到。

![](img/b02fb2fae5a9a48db2455d3730732dd3_12.png)

以下是计算策略收益的关键点：
*   策略收益 = `position` * `returns`。
*   同样，`position` 序列也需要进行 `shift(1)` 操作，以确保使用的是前一天收盘后生成的信号来指导今天的交易。

核心代码如下：
```python
# 假设 position 是交易信号序列（1 或 -1）
strategy_returns = position.shift(1) * returns
```
计算完成后，我们得到了策略的每日收益率序列 `strategy_returns`。

![](img/b02fb2fae5a9a48db2455d3730732dd3_14.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_15.png)

## 累计收益与结果对比

![](img/b02fb2fae5a9a48db2455d3730732dd3_17.png)

我们分别计算了原始增长率和策略增长率。为了比较最终收益，我们需要将这些每日的增长率累计起来，还原成总资产的变化。

![](img/b02fb2fae5a9a48db2455d3730732dd3_18.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_19.png)

这个过程分为两步：
1.  **累计求和**：对每日的增长率序列（`returns` 和 `strategy_returns`）进行累加，使用 `.cumsum()` 方法。
2.  **指数还原**：由于我们之前计算的是比率，累加后需要进行指数运算 `np.exp()` 来还原为实际的价值倍数。

![](img/b02fb2fae5a9a48db2455d3730732dd3_21.png)

核心代码如下：
```python
import numpy as np

![](img/b02fb2fae5a9a48db2455d3730732dd3_23.png)

# 计算累计收益
cumulative_returns = (returns - 1).cumsum() # 注意：通常使用对数收益率，这里为简化使用简单收益率差
cumulative_strategy = (strategy_returns - 1).cumsum()

# 还原为价值（假设初始本金为1）
final_value = np.exp(cumulative_returns)
final_strategy_value = np.exp(cumulative_strategy)

# 或者，如果使用简单收益率相乘：
final_value_simple = (returns).cumprod()
final_strategy_value_simple = (strategy_returns).cumprod()
```
在执行对比后，我们可能会发现策略的最终收益（例如，1元变成5.8元）高于单纯持有股票不操作的收益（例如，1元变成4元）。这直观地展示了双均线策略在本例数据上带来的增值效果。

![](img/b02fb2fae5a9a48db2455d3730732dd3_25.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_26.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_27.png)

## 总结

![](img/b02fb2fae5a9a48db2455d3730732dd3_28.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_29.png)

![](img/b02fb2fae5a9a48db2455d3730732dd3_30.png)

本节课中我们一起学习了交易策略收益分析的全过程。我们首先计算了原始股价的每日增长率，然后通过将交易信号（`position`）与增长率相乘，得到了策略的每日收益率。最后，我们通过对收益率序列进行累计和指数还原，计算并对比了原始持有策略与双均线策略的最终资产价值，从而验证了策略在本案例中的有效性。