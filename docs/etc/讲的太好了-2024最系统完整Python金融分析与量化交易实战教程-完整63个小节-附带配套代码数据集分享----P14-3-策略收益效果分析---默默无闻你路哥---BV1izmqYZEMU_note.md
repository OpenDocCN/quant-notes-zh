# Python金融分析与量化交易实战教程：P14：3-策略收益效果分析 📈

在本节课中，我们将要学习如何计算和比较量化交易策略的收益。我们将通过一个具体的例子，分析双均线策略相对于“什么都不做”的原始持有策略，其收益效果如何。

![](img/dfdb223eacbd85770c904c3edd7c8549_1.png)

上一节我们介绍了如何通过双均线交叉来生成交易信号（`position`）。本节中我们来看看如何利用这些信号来计算策略的实际收益，并与基准收益进行比较。

## 计算原始收益率

![](img/dfdb223eacbd85770c904c3edd7c8549_3.png)

首先，我们需要计算股价的原始日收益率。在量化分析中，我们通常使用对数收益率，因为它具有更好的数学性质（如可加性）。计算对数收益率的公式如下：

**公式：** `log_return = log(price_t / price_{t-1})`

在代码中，我们可以使用Pandas的`shift`函数来获取前一天的股价，并进行计算。

```python
# 假设‘price’是包含股价的Pandas Series
returns = np.log(data['price'] / data['price'].shift(1))
```

执行这段代码后，我们得到了一个名为`returns`的序列，它代表了股价每日的对数增长率。

![](img/dfdb223eacbd85770c904c3edd7c8549_5.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_6.png)

## 计算策略收益率

![](img/dfdb223eacbd85770c904c3edd7c8549_7.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_8.png)

接下来，我们将交易信号应用到收益率上，以计算策略收益率。我们的策略逻辑是：当`position`为1时，我们跟随市场走势（即持有股票）；当`position`为-1时，我们进行反向操作（例如做空或卖出），这意味着我们的收益应与市场原始收益相反。

![](img/dfdb223eacbd85770c904c3edd7c8549_9.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_10.png)

因此，策略收益率的计算公式为：

**公式：** `strategy_returns = position * returns`

![](img/dfdb223eacbd85770c904c3edd7c8549_12.png)

在应用时，需要注意对齐时间。因为交易信号是基于当天收盘后的信息生成的，用于指导第二天的交易。所以我们需要将`position`序列也向后移动一天（`shift(1)`），以确保用前一天的信号来影响今天的收益计算。

![](img/dfdb223eacbd85770c904c3edd7c8549_14.png)

以下是计算策略收益率的关键步骤：

![](img/dfdb223eacbd85770c904c3edd7c8549_15.png)

```python
# 假设‘position’是之前生成的交易信号序列（值为1或-1）
# 将信号序列向后移动一天，以对齐交易日期
aligned_position = data['position'].shift(1)
# 计算策略收益率
strategy_returns = aligned_position * returns
```

## 累计收益与结果比较

![](img/dfdb223eacbd85770c904c3edd7c8549_17.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_18.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_19.png)

计算出每日的策略收益率后，我们需要将其累加起来，得到从期初到期末的总对数收益。然后，通过指数函数将其还原为实际的资产倍数（即1元本金最终变成了多少元）。

以下是计算和比较累计收益的步骤：

![](img/dfdb223eacbd85770c904c3edd7c8549_21.png)

1.  **处理缺失值**：由于使用了`shift`操作，序列开头会产生缺失值（NaN），需要先将其删除或填充。
2.  **累计对数收益**：对处理后的收益率序列进行求和。
3.  **还原为资产倍数**：对累计对数收益取指数。

```python
# 1. 删除缺失值
clean_returns = returns.dropna()
clean_strategy_returns = strategy_returns.dropna()

![](img/dfdb223eacbd85770c904c3edd7c8549_23.png)

# 2. & 3. 计算累计收益并还原为资产倍数
cumulative_return = np.exp(clean_returns.cumsum())
cumulative_strategy_return = np.exp(clean_strategy_returns.cumsum())

# 查看最终结果（最后一天的值）
final_return = cumulative_return.iloc[-1]
final_strategy_return = cumulative_strategy_return.iloc[-1]

print(f"原始持有策略最终资产倍数: {final_return:.2f}")
print(f"双均线策略最终资产倍数: {final_strategy_return:.2f}")
```

![](img/dfdb223eacbd85770c904c3edd7c8549_25.png)

通过比较`final_return`和`final_strategy_return`，我们可以直观地看到策略是否跑赢了简单持有基准。例如，在教程示例中，原始持有策略使1元本金变成了约4元，而应用双均线策略后，1元本金增长到了约5.8元，证明了该策略在回测区间内的有效性。

![](img/dfdb223eacbd85770c904c3edd7c8549_26.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_27.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_28.png)

## 总结

![](img/dfdb223eacbd85770c904c3edd7c8549_29.png)

![](img/dfdb223eacbd85770c904c3edd7c8549_30.png)

本节课中我们一起学习了量化策略收益分析的核心步骤。我们首先计算了股价的原始对数收益率，然后根据交易信号（`position`）计算了策略收益率。最后，通过累计收益并还原为资产倍数，我们将双均线策略的收益效果与原始持有策略进行了对比，从而评估了策略的绩效。这个过程是量化交易回测中评估策略盈利能力的标准方法。