# Python金融量化+股票交易：P11：03-3-策略收益效果分析 📈

在本节课中，我们将学习如何计算和比较量化交易策略的收益。我们将通过一个具体的例子，分析“双均线策略”相对于“什么都不做”（即持有股票）的收益效果。核心在于理解如何计算每日报酬率，并根据策略信号调整收益计算。

![](img/7bde8858190524652d097fd952be8db2_1.png)

---

![](img/7bde8858190524652d097fd952be8db2_3.png)

## 计算每日报酬率

上一节我们介绍了如何生成交易信号。本节中，我们来看看如何基于股价计算每日的报酬率。

报酬率（Return）的计算公式是：**当日股价 / 前一日股价**。在计算时，我们通常使用对数收益率，因为它具有更好的数学性质（如可加性），但在本教程中，我们先从简单的算术收益率开始。

以下是计算每日报酬率的步骤：

![](img/7bde8858190524652d097fd952be8db2_5.png)

![](img/7bde8858190524652d097fd952be8db2_7.png)

1.  获取股价数据列。
2.  使用 `shift(1)` 函数将股价数据向下移动一行，以获取前一天的股价。
3.  用当日股价除以前一日股价，得到每日的增长率。

对应的Python代码如下：
```python
# 假设data是一个包含股价‘Close’列的DataFrame
data[‘returns’] = data[‘Close’] / data[‘Close’].shift(1)
```
执行这段代码后，`data` 数据框中会新增一列 `‘returns’`，代表每日的股价增长率。

![](img/7bde8858190524652d097fd952be8db2_9.png)

---

![](img/7bde8858190524652d097fd952be8db2_11.png)

## 根据策略调整收益

![](img/7bde8858190524652d097fd952be8db2_13.png)

我们已经有了基础的每日报酬率。接下来，需要将上一节生成的交易信号 `position` 应用到收益计算中。

![](img/7bde8858190524652d097fd952be8db2_15.png)

我们的策略逻辑是：当 `position` 为 1 时，代表我们跟随市场趋势（即正常持有）；当 `position` 为 -1 时，代表我们采取与市场相反的操作（例如，在市场下跌时通过做空获利）。因此，策略的每日收益应为 **`position` 信号乘以当日的 `returns`**。

![](img/7bde8858190524652d097fd952be8db2_17.png)

以下是应用策略计算收益的步骤：

1.  确保 `position` 列也进行了 `shift(1)` 操作，以与当日的 `returns` 对齐（因为我们根据前一天的信息决定今天的操作）。
2.  计算策略收益：`strategy_returns = position * returns`。

![](img/7bde8858190524652d097fd952be8db2_19.png)

对应的Python代码如下：
```python
# 对position信号进行移位，与收益对齐
data[‘position’] = data[‘position’].shift(1)
# 计算策略收益
data[‘strategy_returns’] = data[‘position’] * data[‘returns’]
```
计算完成后，`data` 中会新增 `‘strategy_returns’` 列，它反映了在策略指导下的每日收益变化。

![](img/7bde8858190524652d097fd952be8db2_21.png)

---

![](img/7bde8858190524652d097fd952be8db2_23.png)

## 计算并比较累计收益

![](img/7bde8858190524652d097fd952be8db2_25.png)

有了每日的策略收益和原始收益后，我们需要计算在整个回测周期内的总收益，并进行比较。

![](img/7bde8858190524652d097fd952be8db2_27.png)

![](img/7bde8858190524652d097fd952be8db2_29.png)

累计收益的计算方法是：**将每日的增长率连乘**。但因为我们之前计算的是算术收益率，直接连乘即可。在计算前，需要处理可能存在的缺失值（由 `shift` 操作产生）。

以下是计算和比较累计收益的步骤：

1.  使用 `dropna()` 方法清除 `‘returns’` 和 `‘strategy_returns’` 列中的缺失值。
2.  对清理后的两列数据分别进行累乘操作，计算出从期初1元本金到期末的总价值。
3.  比较两个结果。

![](img/7bde8858190524652d097fd952be8db2_31.png)

对应的Python代码如下：
```python
# 清除缺失值
data[[‘returns’, ‘strategy_returns’]] = data[[‘returns’, ‘strategy_returns’]].dropna()

![](img/7bde8858190524652d097fd952be8db2_33.png)

# 计算累计收益：累乘每日增长率
cumulative_returns = (data[‘returns’]).prod()
cumulative_strategy_returns = (data[‘strategy_returns’]).prod()

![](img/7bde8858190524652d097fd952be8db2_35.png)

![](img/7bde8858190524652d097fd952be8db2_37.png)

print(f“什么都不做的累计收益: {cumulative_returns:.2f}“)
print(f“双均线策略的累计收益: {cumulative_strategy_returns:.2f}“)
```
执行代码后，我们可以得到类似的结果：
-   什么都不做：1元变成了约4元。
-   使用双均线策略：1元变成了约5.8元。

这个对比直观地展示了在该段历史数据上，双均线策略带来了更高的收益。

---

![](img/7bde8858190524652d097fd952be8db2_39.png)

本节课中我们一起学习了量化策略收益分析的核心流程：从计算每日报酬率开始，到根据交易信号调整每日收益，最后计算并比较策略的累计收益。通过将策略收益与基准（买入持有）收益对比，我们可以定量评估一个交易策略的历史表现效果。