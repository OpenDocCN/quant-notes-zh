# 量化交易完全可自学教程：P71：9. 8.10.3-策略收益效果分析P10 📈

在本节课中，我们将学习如何计算和比较量化策略的最终收益。我们将通过一个具体的例子，演示如何将每日收益率累加，并还原为最终的资金增长倍数，从而评估策略的有效性。

![](img/37da4332c07ff7b60a697cc4dde6acef_1.png)

上一节我们介绍了如何计算每日收益率，本节中我们来看看如何将这些收益率累积起来，并与“买入并持有”的原始策略进行对比。

![](img/37da4332c07ff7b60a697cc4dde6acef_3.png)

## 计算每日收益率

首先，我们需要计算股价的每日收益率。这是一个累加过程的基础，我们不能直接对价格进行累加，而是需要引入对数收益率进行计算。

在我们的分析中，收益率是针对股价计算的。具体方法是计算当前股价除以前一日股价的结果。

以下是计算每日收益率的代码：

```python
returns = np.log(data['Close'] / data['Close'].shift(1))
```

![](img/37da4332c07ff7b60a697cc4dde6acef_5.png)

这段代码执行后，`returns`变量就存储了每日的增长率（对数收益率）。

![](img/37da4332c07ff7b60a697cc4dde6acef_7.png)

## 结合策略信号计算策略收益率

![](img/37da4332c07ff7b60a697cc4dde6acef_9.png)

接下来，我们需要按照我们的交易策略来计算收益率。我们的策略基于一个`position`信号，该信号指示我们是应该跟随市场（position=1）还是反向操作（position=-1）。

现在来看我们的策略逻辑。策略的核心在于，当`position`信号为-1时，我们假设能够预知市场的下跌并反向操作，从而将亏损转化为盈利。这在实际中可能难以实现，但在此我们用于演示。

![](img/37da4332c07ff7b60a697cc4dde6acef_11.png)

![](img/37da4332c07ff7b60a697cc4dde6acef_13.png)

以下是结合策略信号计算每日策略收益的步骤：

我们需要将之前计算出的`position`信号与每日收益率相乘。`position`为1代表跟随市场走势，`position`为-1代表进行反向操作。

![](img/37da4332c07ff7b60a697cc4dde6acef_15.png)

具体计算公式如下：

![](img/37da4332c07ff7b60a697cc4dde6acef_17.png)

**策略日收益率 = position * 日收益率**

在应用时，需要注意`position`信号也需要进行`shift(1)`操作，以确保使用的是前一交易日结束时生成的信号来指导当日的交易。

![](img/37da4332c07ff7b60a697cc4dde6acef_19.png)

以下是实现代码：

![](img/37da4332c07ff7b60a697cc4dde6acef_21.png)

```python
# 假设 position 是之前计算好的交易信号序列，同样经过 shift(1) 处理
strategy_returns = position.shift(1) * returns
```

![](img/37da4332c07ff7b60a697cc4dde6acef_23.png)

计算完成后，我们可以查看`strategy_returns`序列。由于数据前期大部分时间`position`可能为1，所以前期的策略收益率与原始收益率看起来会相同。

![](img/37da4332c07ff7b60a697cc4dde6acef_25.png)

## 计算并比较最终收益

![](img/37da4332c07ff7b60a697cc4dde6acef_27.png)

我们最终想看到的是，从期初投入1元钱，到期末能变成多少钱。这就需要将每日的收益率进行累加。

![](img/37da4332c07ff7b60a697cc4dde6acef_29.png)

对于对数收益率，将所有日期的值相加，再通过指数函数还原，即可得到最终的资金增长倍数。

![](img/37da4332c07ff7b60a697cc4dde6acef_31.png)

以下是计算最终收益的步骤：

首先，我们需要处理数据中的缺失值（由`shift`操作产生），然后对收益率序列进行求和，最后通过指数函数还原。

具体实现代码如下：

![](img/37da4332c07ff7b60a697cc4dde6acef_33.png)

```python
# 1. 删除缺失值
returns_clean = returns.dropna()
strategy_returns_clean = strategy_returns.dropna()

![](img/37da4332c07ff7b60a697cc4dde6acef_35.png)

# 2. 对对数收益率求和
total_log_return = returns_clean.sum()
total_strategy_log_return = strategy_returns_clean.sum()

![](img/37da4332c07ff7b60a697cc4dde6acef_37.png)

# 3. 通过指数函数还原为最终收益倍数
final_return = np.exp(total_log_return)
final_strategy_return = np.exp(total_strategy_log_return)

![](img/37da4332c07ff7b60a697cc4dde6acef_39.png)

print(f"原始‘买入并持有’策略最终收益倍数: {final_return:.2f}")
print(f"双均线策略最终收益倍数: {final_strategy_return:.2f}")
```

执行上述代码后，我们可以进行比较。例如，结果显示：
- 原始“买入并持有”策略：1元钱最终变成了约4元钱。
- 应用双均线策略后：1元钱最终变成了约5.8元钱。

![](img/37da4332c07ff7b60a697cc4dde6acef_41.png)

这表明，在我们演示的这个案例中，双均线策略获得了比简单持有更高的收益。

![](img/37da4332c07ff7b60a697cc4dde6acef_43.png)

本节课中我们一起学习了量化策略收益分析的核心步骤：从计算每日对数收益率，到结合策略信号得出策略日收益率，最后通过累加和还原得到策略的最终收益倍数，并与基准策略进行对比。这是评估一个量化策略是否有效的关键环节。