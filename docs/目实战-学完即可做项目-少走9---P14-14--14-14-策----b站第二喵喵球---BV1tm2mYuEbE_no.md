# 量化交易实战：14：策略收益效果分析 📈

在本节课中，我们将学习如何计算和比较量化策略的收益。我们将通过一个具体的例子，分析在应用“双均线策略”后，投资收益相较于“买入并持有”策略的变化。核心在于理解如何计算每日报酬率，并基于策略信号调整收益计算。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_1.png)

## 计算基础报酬率

![](img/74f44ffadc37829fd231b87aaa9ff6a7_3.png)

上一节我们介绍了股价数据的处理，本节中我们来看看如何计算每日的基础报酬率。报酬率反映了资产价格每日的变化情况。

计算每日报酬率的公式为：
`当日收盘价 / 前一日收盘价`

在Python的pandas库中，我们可以使用`.shift(1)`方法获取前一天的数据。以下是计算代码：

![](img/74f44ffadc37829fd231b87aaa9ff6a7_5.png)

```python
# 假设data是一个包含‘Close’列的DataFrame
data[‘returns’] = data[‘Close’] / data[‘Close’].shift(1)
```

![](img/74f44ffadc37829fd231b87aaa9ff6a7_7.png)

执行这段代码后，`data[‘returns’]`列就存储了每日的增长率（例如，1.02表示上涨2%）。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_9.png)

![](img/74f44ffadc37829fd231b87aaa9ff6a7_11.png)

## 应用策略调整收益

仅仅计算市场自然增长的收益还不够。我们的策略（例如双均线策略）会生成交易信号，指示我们应该买入、卖出或反向操作。接下来，我们将根据策略信号来调整收益计算。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_13.png)

![](img/74f44ffadc37829fd231b87aaa9ff6a7_15.png)

我们假设已经根据策略生成了一个名为`position`的信号列。在这个例子中：
*   `position = 1` 表示遵循市场原始走势（即使用我们上面计算的`returns`）。
*   `position = -1` 表示进行反向操作（即在市场下跌时我们盈利，上涨时我们亏损，这需要将`returns`取反）。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_17.png)

因此，策略下的每日收益计算公式为：
`策略日收益 = position * returns`

同样，为了与收益计算对齐，`position`列也需要进行`.shift(1)`操作，使用前一天的信号来决定今天的操作。代码如下：

![](img/74f44ffadc37829fd231b87aaa9ff6a7_19.png)

```python
# 计算策略收益
data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
```

![](img/74f44ffadc37829fd231b87aaa9ff6a7_21.png)

## 计算并比较累计收益

![](img/74f44ffadc37829fd231b87aaa9ff6a7_23.png)

在得到调整后的每日收益后，我们需要计算一段时间的总收益，并比较策略收益与基础收益的差异。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_25.png)

![](img/74f44ffadc37829fd231b87aaa9ff6a7_27.png)

首先，我们需要处理数据中的缺失值（由`.shift()`操作产生）。然后，将每日的增长率累加起来。由于我们之前计算的是比率，直接累加并不符合金融逻辑，我们需要先取对数进行累加，再通过指数函数还原。以下是完整的计算步骤：

![](img/74f44ffadc37829fd231b87aaa9ff6a7_29.png)

1.  **处理缺失值**：删除`returns`和`strategy`列中的NaN值。
2.  **计算对数收益**：对每日收益取对数（`np.log`），这样可以将乘法关系转换为加法关系便于累加。
3.  **累加收益**：将对数收益进行求和（`.sum()`），得到整个时间段的总对数收益。
4.  **还原为总收益**：对总对数收益取指数（`np.exp`），得到最终的总资产倍数（即1元本金最终变为多少元）。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_31.png)

![](img/74f44ffadc37829fd231b87aaa9ff6a7_33.png)

以下是实现代码：

![](img/74f44ffadc37829fd231b87aaa9ff6a7_35.png)

```python
import numpy as np

# 1. 删除缺失值
data[[‘returns’, ‘strategy’]] = data[[‘returns’, ‘strategy’]].dropna()

![](img/74f44ffadc37829fd231b87aaa9ff6a7_37.png)

# 2 & 3 & 4. 计算总收益
total_return = np.exp(data[‘returns’].apply(np.log).sum())
total_strategy_return = np.exp(data[‘strategy’].apply(np.log).sum())

![](img/74f44ffadc37829fd231b87aaa9ff6a7_39.png)

print(f“买入持有总收益：{total_return:.2f}”)
print(f“策略总收益：{total_strategy_return:.2f}”)
```

![](img/74f44ffadc37829fd231b87aaa9ff6a7_41.png)

## 结果解读

![](img/74f44ffadc37829fd231b87aaa9ff6a7_43.png)

![](img/74f44ffadc37829fd231b87aaa9ff6a7_45.png)

执行上述计算后，我们可以得到两个数值。例如：
*   `买入持有总收益：4.00`
*   `策略总收益：5.80`

![](img/74f44ffadc37829fd231b87aaa9ff6a7_47.png)

这个结果意味着：
*   如果采用简单的“买入并持有”策略，最初投资的1元钱最终会变成4元钱。
*   如果应用我们的“双均线策略”，最初投资的1元钱最终会变成5.8元钱。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_49.png)

显然，在该历史数据回测中，我们的量化策略（5.8元）获得了比市场基准收益（4.0元）更高的回报，展现了策略的有效性。

![](img/74f44ffadc37829fd231b87aaa9ff6a7_51.png)

---

![](img/74f44ffadc37829fd231b87aaa9ff6a7_53.png)

![](img/74f44ffadc37829fd231b87aaa9ff6a7_55.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们从计算每日基础报酬率开始，接着学习了如何根据策略信号（`position`）调整每日收益，最后通过处理缺失值、对数累加和指数还原，计算并比较了策略与基准的最终累计收益。通过这个完整的分析流程，你可以客观地评估一个交易策略在历史数据上的表现。