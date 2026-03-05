# Python金融量化交易：14：策略收益效果分析 📈

在本节课中，我们将要学习如何计算和比较量化策略的收益。我们将通过一个具体的例子，分析在应用双均线策略后，投资收益相较于“买入并持有”策略的变化。核心在于理解如何计算每日收益率，如何根据策略信号调整收益，以及如何将累计的对数收益率还原为最终的实际收益。

![](img/9b67cff3a5afbcee26ed950da0ffcd14_1.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_3.png)

上一节我们介绍了如何生成交易信号，本节中我们来看看如何基于这些信号计算策略的最终收益。

## 计算基础收益率

首先，我们需要计算股票每日的基础收益率。在量化分析中，我们通常使用对数收益率，因为它具有更好的统计特性（如可加性）。计算方式是用当日股价除以前一日股价，然后取自然对数。

![](img/9b67cff3a5afbcee26ed950da0ffcd14_5.png)

以下是计算对数收益率的公式和代码：

![](img/9b67cff3a5afbcee26ed950da0ffcd14_7.png)

**公式**：`log_return = ln(P_t / P_{t-1})`

![](img/9b67cff3a5afbcee26ed950da0ffcd14_9.png)

**代码**：
```python
# 假设‘data’是一个包含股价‘Close’列的DataFrame
data['returns'] = np.log(data['Close'] / data['Close'].shift(1))
```
在这段代码中，`data[‘Close’].shift(1)` 表示将股价序列向下移动一位，从而得到前一天的股价。`np.log` 是计算自然对数的函数。

![](img/9b67cff3a5afbcee26ed950da0ffcd14_11.png)

## 根据策略调整收益

![](img/9b67cff3a5afbcee26ed950da0ffcd14_13.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_15.png)

接下来，我们需要根据之前生成的交易信号来调整收益。我们的策略信号 `position` 是一个序列，其中值为1表示跟随原始趋势（即使用基础收益率），值为-1表示进行反向操作（即使用基础收益率的相反数）。

以下是策略收益的计算逻辑：

![](img/9b67cff3a5afbcee26ed950da0ffcd14_17.png)

**公式**：`strategy_returns = position * returns`

![](img/9b67cff3a5afbcee26ed950da0ffcd14_19.png)

**代码**：
```python
# 假设‘position’是之前计算出的策略信号列
# 注意：信号也需要进行shift操作，以确保使用前一天的信号来决定今天的操作
data['strategy'] = data['position'].shift(1) * data['returns']
```
这里的关键点是 `data[‘position’].shift(1)`。这确保了我们在第t天使用的是第t-1天收盘后产生的信号，避免了使用未来数据，这是回测中必须遵守的规则。

![](img/9b67cff3a5afbcee26ed950da0ffcd14_21.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_23.png)

## 计算并比较最终收益

![](img/9b67cff3a5afbcee26ed950da0ffcd14_25.png)

计算出每日的策略收益率后，我们需要将它们累加起来，得到总的对数收益率。最后，通过指数函数将总对数收益率还原为最终的资金倍数（即1元本金最终变为多少元）。

![](img/9b67cff3a5afbcee26ed950da0ffcd14_27.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_29.png)

以下是计算并比较最终收益的步骤：

1.  **累加收益率**：对 `‘returns’` 和 `‘strategy’` 列分别求和，得到总对数收益率。
2.  **还原为资金倍数**：对总对数收益率取指数（`np.exp`），得到最终收益倍数。

**代码**：
```python
# 计算总对数收益率
total_log_return = data[['returns', 'strategy']].sum()

![](img/9b67cff3a5afbcee26ed950da0ffcd14_31.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_33.png)

# 将总对数收益率还原为最终收益倍数
final_value = np.exp(total_log_return)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_35.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_37.png)

print(final_value)
```
执行以上代码后，我们会得到两个数字。例如：
*   `returns`: 4.0 （表示不采取策略，1元本金最终变为4元）
*   `strategy`: 5.8 （表示采取双均线策略，1元本金最终变为5.8元）

这个比较清晰地展示了策略带来的超额收益。

![](img/9b67cff3a5afbcee26ed950da0ffcd14_39.png)

## 总结

![](img/9b67cff3a5afbcee26ed950da0ffcd14_41.png)

![](img/9b67cff3a5afbcee26ed950da0ffcd14_43.png)

本节课中我们一起学习了量化策略收益分析的核心步骤。我们首先计算了股票的基础对数收益率，然后根据双均线策略产生的交易信号对收益率进行调整，最后通过累加和指数还原，计算并比较了策略与基准的最终收益效果。结果显示，在此例中，双均线策略获得了比简单持有更高的收益。这个过程是量化交易回测中评估策略有效性的基础。