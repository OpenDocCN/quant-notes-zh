# Python金融分析与量化交易实战教程：P11：3-策略收益效果分析 📈

![](img/8c18b906a027db5f65c70dcbca64c31e_1.png)

## 概述
在本节课中，我们将学习如何计算和比较量化交易策略的收益。我们将通过一个具体的例子，分析“双均线策略”相对于“什么都不做”的原始投资，其收益效果有何不同。核心在于理解如何计算每日收益率，并根据策略信号调整收益计算方式，最终评估策略的有效性。

![](img/8c18b906a027db5f65c70dcbca64c31e_3.png)

---

## 计算原始收益率
上一节我们介绍了如何生成交易信号。本节中，我们来看看如何基于这些信号计算策略的最终收益。

首先，我们需要计算股价的原始日收益率。收益率不能通过简单累加计算，通常使用对数收益率，其公式为：
`log_return = log(price_t / price_{t-1})`
但在本教程中，为简化理解，我们直接计算每日的简单收益率。

![](img/8c18b906a027db5f65c70dcbca64c31e_5.png)

![](img/8c18b906a027db5f65c70dcbca64c31e_7.png)

以下是计算原始收益率的步骤：
1.  获取股价数据。
2.  计算当前日股价除以前一日股价的比值。
3.  使用 `shift(1)` 方法将数据下移一行，以正确对齐“前一日”的数据。

![](img/8c18b906a027db5f65c70dcbca64c31e_9.png)

```python
# 假设 `data` 是一个包含股价的DataFrame，列名为 ‘Close‘
data[‘returns‘] = data[‘Close‘] / data[‘Close‘].shift(1)
```
执行这段代码后，`data` 数据框中会新增一列 `returns`，它代表了每日的增长率（例如，1.02 表示上涨2%）。

![](img/8c18b906a027db5f65c70dcbca64c31e_11.png)

---

![](img/8c18b906a027db5f65c70dcbca64c31e_13.png)

![](img/8c18b906a027db5f65c70dcbca64c31e_15.png)

## 根据策略信号调整收益
我们已经有了原始收益率和代表交易信号的 `position` 列。现在，我们需要将两者结合起来，计算策略的收益率。

![](img/8c18b906a027db5f65c70dcbca64c31e_17.png)

我们的策略逻辑是：当 `position` 为 1 时，表示我们预测市场将上涨或下跌，并采取与原始走势相反的操作（例如，在预测下跌时做空）。因此，策略收益率应为 `position` 与 `returns` 的乘积。但需要注意，`position` 信号也需要进行 `shift(1)` 操作，以确保使用前一日生成的信号来指导当日的交易。

以下是计算策略收益的步骤：
1.  确保 `position` 列已进行 `shift(1)` 操作，以对齐交易日。
2.  将调整后的 `position` 与 `returns` 相乘，得到策略的每日收益率。

![](img/8c18b906a027db5f65c70dcbca64c31e_19.png)

![](img/8c18b906a027db5f65c70dcbca64c31e_21.png)

```python
# 假设 `position` 列已存在于 data 中，并已进行 shift(1) 操作
data[‘strategy‘] = data[‘position‘].shift(1) * data[‘returns‘]
```
计算完成后，`data` 中会新增 `strategy` 列，它代表了应用策略后的每日收益率。

![](img/8c18b906a027db5f65c70dcbca64c31e_23.png)

---

![](img/8c18b906a027db5f65c70dcbca64c31e_25.png)

![](img/8c18b906a027db5f65c70dcbca64c31e_27.png)

## 计算并比较累计收益
为了评估策略的长期效果，我们需要计算累计收益，即观察初始的1元钱最终变成了多少钱。

![](img/8c18b906a027db5f65c70dcbca64c31e_29.png)

以下是计算和比较累计收益的步骤：
1.  分别对 `returns` 列和 `strategy` 列的每日收益率进行累加（使用 `.cumsum()` 方法）。
2.  由于我们之前计算的是简单收益率，累加后即可得到累计收益的序列。但需注意处理可能存在的缺失值（NaN）。
3.  比较两种方式下最终的总收益数值。

```python
# 计算累计收益
cumulative_returns = (data[‘returns‘] - 1).cumsum()
cumulative_strategy = (data[‘strategy‘] - 1).cumsum()

![](img/8c18b906a027db5f65c70dcbca64c31e_31.png)

![](img/8c18b906a027db5f65c70dcbca64c31e_33.png)

# 或者，直接看最终的总收益（假设从1开始）
total_return = (data[‘returns‘] - 1).sum()
total_strategy_return = (data[‘strategy‘] - 1).sum()

![](img/8c18b906a027db5f65c70dcbca64c31e_35.png)

![](img/8c18b906a027db5f65c70dcbca64c31e_37.png)

print(f“原始投资总收益：{total_return:.2f}“)
print(f“策略投资总收益：{total_strategy_return:.2f}“)
```
通过比较 `total_return` 和 `total_strategy_return`，我们可以直观地看到策略是否带来了超额收益。例如，原始收益为4（1元变4元），而策略收益为5.8（1元变5.8元），则说明该策略是有效的。

---

![](img/8c18b906a027db5f65c70dcbca64c31e_39.png)

## 总结
本节课中我们一起学习了量化策略收益分析的核心流程。我们首先计算了资产的原始日收益率，然后根据预先定义的双均线策略信号（`position`）对收益率进行调整，得到了策略的日收益率序列。最后，通过累加日收益率，我们计算并比较了“买入持有”原始资产与应用交易策略两种情况的最终总收益，从而定量地评估了该策略的绩效表现。这个过程是量化交易回测中结果评估的基础。