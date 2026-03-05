# Python金融量化与股票交易：P11：03-3：策略收益效果分析 📈

![](img/62a4c814e86b14bc76ad7c27c74deadc_1.png)

在本节课中，我们将学习如何计算和比较股票策略的收益。我们将从计算基础收益率开始，然后应用我们之前构建的双均线策略，最后对比策略收益与原始收益的差异。

![](img/62a4c814e86b14bc76ad7c27c74deadc_3.png)

---

上一节我们介绍了如何通过双均线策略生成交易信号（`position`）。本节中，我们来看看如何基于这些信号计算策略的实际收益。

![](img/62a4c814e86b14bc76ad7c27c74deadc_5.png)

首先，我们需要计算股票每日的原始收益率。收益率不能直接累加，我们通常使用对数收益率进行计算。

![](img/62a4c814e86b14bc76ad7c27c74deadc_7.png)

以下是计算对数收益率的步骤：
1.  获取股价数据。
2.  计算当前股价除以前一日股价的结果。
3.  对这个比值取自然对数。

![](img/62a4c814e86b14bc76ad7c27c74deadc_9.png)

在代码中，这可以通过`shift`函数实现：
```python
# 计算对数收益率
data[‘returns’] = np.log(data[‘Close’] / data[‘Close’].shift(1))
```
执行这段代码后，`data[‘returns’]`列就存储了每日的增长率。

![](img/62a4c814e86b14bc76ad7c27c74deadc_11.png)

![](img/62a4c814e86b14bc76ad7c27c74deadc_13.png)

---

![](img/62a4c814e86b14bc76ad7c27c74deadc_15.png)

接下来，我们将策略信号应用到收益率上。我们的策略核心是：在`position`标记为-1的区间（即短期均线下穿长期均线的看跌区间），我们采取与市场走势相反的操作，将亏损转为盈利。

![](img/62a4c814e86b14bc76ad7c27c74deadc_17.png)

以下是应用策略计算策略收益的步骤：
1.  确保`position`列也进行了`shift(1)`操作，以与收益率对齐，代表基于前一天信号执行的操作。
2.  将调整后的`position`值与`returns`相乘。当`position`为1时，收益与市场一致；当`position`为-1时，收益与市场相反。

![](img/62a4c814e86b14bc76ad7c27c74deadc_19.png)

对应的代码如下：
```python
# 应用策略信号计算策略收益
data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
```
计算完成后，`data[‘strategy’]`列就代表了策略每日的收益率。

![](img/62a4c814e86b14bc76ad7c27c74deadc_21.png)

![](img/62a4c814e86b14bc76ad7c27c74deadc_23.png)

---

![](img/62a4c814e86b14bc76ad7c27c74deadc_25.png)

![](img/62a4c814e86b14bc76ad7c27c74deadc_27.png)

为了对比长期效果，我们需要将每日的收益率累加起来，并还原为最终的资金增长倍数。由于我们使用的是对数收益率，累加就是求和，再通过指数函数还原。

![](img/62a4c814e86b14bc76ad7c27c74deadc_29.png)

以下是计算最终收益的步骤：
1.  分别对原始收益率列(`returns`)和策略收益率列(`strategy`)进行求和。这代表了整个时间段内的总对数收益。
2.  使用`np.exp`函数将总对数收益还原为资金倍数（即1元本金最终变为多少元）。
3.  处理可能存在的缺失值（NaN）。

实现代码如下：
```python
# 计算总收益并还原
total_return = np.exp(data[[‘returns’, ‘strategy’]].sum()) - 1  # 假设计算简单收益
# 或使用累计乘积计算资金曲线
data[‘cumulative_returns’] = (1 + data[‘returns’]).cumprod()
data[‘cumulative_strategy’] = (1 + data[‘strategy’]).cumprod()
```
执行计算后，我们可以得到两个关键数字：什么都不做的最终资金倍数，以及执行双均线策略后的最终资金倍数。

![](img/62a4c814e86b14bc76ad7c27c74deadc_31.png)

![](img/62a4c814e86b14bc76ad7c27c74deadc_33.png)

在本案例中，结果显示：
*   **原始收益**：1元本金最终变为约4元。
*   **策略收益**：1元本金最终变为约5.8元。

![](img/62a4c814e86b14bc76ad7c27c74deadc_35.png)

![](img/62a4c814e86b14bc76ad7c27c74deadc_37.png)

这表明，在此段数据上，应用双均线策略获得了比单纯持有股票更高的收益。

---

![](img/62a4c814e86b14bc76ad7c27c74deadc_39.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们从计算基础的股票对数收益率开始，然后将双均线策略生成的交易信号应用到收益率上，最后通过累加和还原操作，对比了策略收益与原始市场收益。结果显示，策略在此案例中取得了更优的表现。这个过程是量化交易中评估策略有效性的核心环节。