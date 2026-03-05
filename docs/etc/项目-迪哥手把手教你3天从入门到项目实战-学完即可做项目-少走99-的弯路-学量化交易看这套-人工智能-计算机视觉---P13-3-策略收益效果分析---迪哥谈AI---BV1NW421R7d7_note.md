# 金融量化分析：P13：3-策略收益效果分析 📈

![](img/c0cf8e39a87ad9e56fe74096672882e8_1.png)

在本节课中，我们将学习如何计算和比较量化策略的收益。我们将从计算基础收益率开始，然后结合我们之前构建的双均线策略，计算策略调整后的收益，最后通过对比来评估策略的有效性。

## 计算基础收益率

![](img/c0cf8e39a87ad9e56fe74096672882e8_3.png)

上一节我们介绍了双均线策略的信号生成。本节中，我们来看看如何计算策略的最终收益。首先，我们需要计算股票的基础收益率。

在量化分析中，我们通常使用对数收益率，因为它具有更好的数学性质（如可加性）。计算对数收益率的公式如下：

**公式：** `log_return = log(price_t / price_{t-1})`

在Python的pandas库中，我们可以通过以下代码实现：

```python
# 假设‘price’是包含股价的DataFrame列
returns = np.log(data['price'] / data['price'].shift(1))
```

![](img/c0cf8e39a87ad9e56fe74096672882e8_5.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_6.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_7.png)

这段代码计算了每日股价相对于前一日股价的对数增长率。`shift(1)`函数将数据向下移动一行，从而让当前价格除以前一日的价格。

![](img/c0cf8e39a87ad9e56fe74096672882e8_8.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_9.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_10.png)

## 结合策略计算调整后收益

基础收益率反映了市场的原始走势。现在，我们需要将我们的交易策略融入其中。我们的双均线策略会生成一个`position`信号，其值为1（做多）或-1（做空）。

![](img/c0cf8e39a87ad9e56fe74096672882e8_12.png)

策略的核心思想是：当`position`为1时，我们跟随市场原始走势；当`position`为-1时，我们反向操作，即市场下跌时我们盈利。因此，策略的每日收益率应为原始收益率乘以当日的`position`信号。

![](img/c0cf8e39a87ad9e56fe74096672882e8_14.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_15.png)

**公式：** `strategy_return = position * return`

同样，在应用`position`时，我们需要使用`shift(1)`来确保使用的是前一交易日结束时确定的仓位来影响当日的收益。代码如下：

![](img/c0cf8e39a87ad9e56fe74096672882e8_17.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_18.png)

```python
# 假设‘position’是策略生成的仓位信号列
strategy_returns = data['position'].shift(1) * returns
```

![](img/c0cf8e39a87ad9e56fe74096672882e8_19.png)

## 比较策略收益与基准收益

![](img/c0cf8e39a87ad9e56fe74096672882e8_21.png)

计算出策略的每日收益率后，我们需要将其与什么都不做的基准收益（即单纯持有股票）进行比较，以评估策略的优劣。

以下是计算和比较总收益的步骤：

![](img/c0cf8e39a87ad9e56fe74096672882e8_23.png)

1.  **处理缺失值**：由于使用了`shift`操作，数据首行会产生缺失值（NaN），我们需要将其删除。
    ```python
    returns = returns.dropna()
    strategy_returns = strategy_returns.dropna()
    ```
2.  **计算累计收益**：将每日的对数收益率相加，得到整个时间段的总对数收益。
    ```python
    total_log_return_baseline = returns.sum()
    total_log_return_strategy = strategy_returns.sum()
    ```
3.  **还原为实际收益**：将对数总收益通过指数函数还原为实际的资产倍数（即1元钱最终变成了多少元）。
    **公式：** `final_value = exp(total_log_return)`
    ```python
    final_value_baseline = np.exp(total_log_return_baseline)
    final_value_strategy = np.exp(total_log_return_strategy)
    ```

最后，对比`final_value_baseline`和`final_value_strategy`。如果策略有效，策略的最终价值应高于基准价值。

![](img/c0cf8e39a87ad9e56fe74096672882e8_25.png)

## 本节总结

![](img/c0cf8e39a87ad9e56fe74096672882e8_26.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_27.png)

![](img/c0cf8e39a87ad9e56fe74096672882e8_28.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们首先计算了股票的对数收益率作为基准。然后，我们将双均线策略生成的仓位信号与收益率结合，得到了策略调整后的收益率序列。最后，我们通过累计计算并还原为最终资产价值，将策略收益与基准收益进行对比，从而定量地评估了策略的盈利能力。这个过程是量化交易回测中结果评估的核心环节。