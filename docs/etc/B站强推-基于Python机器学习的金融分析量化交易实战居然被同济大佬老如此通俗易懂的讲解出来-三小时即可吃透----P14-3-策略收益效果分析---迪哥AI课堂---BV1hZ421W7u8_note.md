# 机器学习金融分析：P14：3-策略收益效果分析 📈

![](img/aa15dce3abec1d8f0351e4a3918b03a9_1.png)

在本节课中，我们将学习如何计算和比较交易策略的最终收益。我们将从计算股价的日增长率开始，然后结合我们之前构建的“双均线策略”信号，计算策略的模拟收益，并与“什么都不做”的买入持有策略进行对比，以评估策略的有效性。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_3.png)

上一节我们介绍了如何通过移动平均线交叉来生成交易信号（`position`）。本节中我们来看看如何将这些信号转化为具体的收益数值，并进行效果评估。

## 计算股价日增长率

首先，我们需要计算股价的日增长率。在金融分析中，为了便于累加计算，我们通常使用对数收益率。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_5.png)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_7.png)

以下是计算对数收益率的步骤：

![](img/aa15dce3abec1d8f0351e4a3918b03a9_9.png)

1.  获取股价序列。我们使用变量 `data['Adj Close']` 来表示调整后的收盘价。
2.  计算对数收益率。公式为：`log(当前价格 / 昨日价格)`。在代码中，我们通过 `shift(1)` 操作来获取前一天的股价。

对应的Python代码如下：
```python
# 计算对数收益率
data['return'] = np.log(data['Adj Close'] / data['Adj Close'].shift(1))
```
执行这段代码后，`data` 数据框中会新增一列 `‘return’`，它代表了股价每日的增长比率。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_11.png)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_13.png)

## 结合策略计算策略收益率

![](img/aa15dce3abec1d8f0351e4a3918b03a9_15.png)

接下来，我们需要将策略信号应用到收益率上。我们的 `position` 列已经指明了每一天应该采取的行动：`1` 代表跟随市场（做多），`-1` 代表反向市场（做空，即认为股价会跌）。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_17.png)

以下是计算策略收益的逻辑：

![](img/aa15dce3abec1d8f0351e4a3918b03a9_19.png)

1.  **策略核心**：策略收益 = 当日信号(`position`) × 当日市场收益率(`return`)。
    *   当 `position` 为 `1` 时，策略收益等于市场收益。
    *   当 `position` 为 `-1` 时，策略收益等于市场收益的相反数，这模拟了“做空”操作——在市场下跌时我们盈利。
2.  **注意点**：为了使用前一日收盘后生成的信号来指导第二天的交易，我们需要对 `position` 列也进行 `shift(1)` 操作，确保信号的应用没有未来数据。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_21.png)

对应的Python代码如下：
```python
# 计算策略收益率，注意对position进行shift操作以避免未来函数
data['strategy'] = data['position'].shift(1) * data['return']
```
计算完成后，`data` 数据框中会新增 `‘strategy’` 列，它代表了按照我们的双均线策略每日所能获得的收益率。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_23.png)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_25.png)

## 计算并比较最终累积收益

![](img/aa15dce3abec1d8f0351e4a3918b03a9_27.png)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_29.png)

现在，我们有了两条收益率序列：原始市场收益率(`return`)和策略收益率(`strategy`)。我们需要将它们从对数形式还原为累积收益，以比较“1元钱”最终变成了多少。

以下是计算和比较的步骤：

1.  **处理缺失值**：由于使用了 `shift` 操作，数据首行会产生缺失值(`NaN`)，我们需要使用 `dropna()` 方法将其删除。
2.  **计算累积收益**：将对数收益率累加 (`sum()`)，然后通过指数函数 `np.exp()` 将其还原为最终的资产倍数。公式为：`最终资产 = exp(收益率总和)`。
3.  **结果对比**：分别计算市场收益和策略收益的最终资产值，并进行比较。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_31.png)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_33.png)

对应的Python代码如下：
```python
# 删除缺失值
data.dropna(inplace=True)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_35.png)

# 计算累积收益：先求和，再取指数还原
cumulative_return_market = np.exp(data['return'].sum())
cumulative_return_strategy = np.exp(data['strategy'].sum())

![](img/aa15dce3abec1d8f0351e4a3918b03a9_37.png)

print(f“买入持有策略最终资产： {cumulative_return_market:.2f}”)
print(f“双均线策略最终资产： {cumulative_return_strategy:.2f}”)
```
执行代码后，我们可能会得到类似这样的结果：
> 买入持有策略最终资产： 4.00
> 双均线策略最终资产： 5.80

![](img/aa15dce3abec1d8f0351e4a3918b03a9_39.png)

这个结果表明，在回测周期内，简单的买入持有策略使1元钱变成了4元。而应用我们的双均线策略后，1元钱变成了5.8元，获得了更高的收益，初步证明了该策略在历史数据上的有效性。

![](img/aa15dce3abec1d8f0351e4a3918b03a9_41.png)

![](img/aa15dce3abec1d8f0351e4a3918b03a9_43.png)

本节课中我们一起学习了如何量化分析一个交易策略的收益。我们首先计算了股价的对数收益率，然后通过将策略信号（做多/做空）与收益率相乘，得到了策略的收益率序列。最后，我们通过累加和指数还原，计算并对比了策略与基准（买入持有）的最终累积收益。这个过程是量化策略回测中最核心的绩效评估环节之一。