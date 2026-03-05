# Python金融分析与量化交易实战：13.3：策略收益效果分析 📈

在本节课中，我们将学习如何计算和比较量化策略的收益。我们将基于股价数据，通过计算对数收益率，并结合之前构建的双均线策略信号，来评估策略的实际盈利效果。

![](img/aebb3b9197c83cc1d6131b8268608cd3_1.png)

![](img/aebb3b9197c83cc1d6131b8268608cd3_3.png)

---

上一节我们介绍了如何通过双均线交叉来生成交易信号。本节中，我们来看看如何将这些信号转化为具体的收益数据，并与“买入并持有”的原始策略进行对比。

首先，我们需要计算股价的日收益率。在量化分析中，为了便于累加计算，我们通常使用对数收益率。其计算公式为：

`log_return = log(price_t / price_{t-1})`

![](img/aebb3b9197c83cc1d6131b8268608cd3_5.png)

在Python中，我们可以通过Pandas的`shift`方法来实现这个计算。

![](img/aebb3b9197c83cc1d6131b8268608cd3_7.png)

以下是计算对数收益率的步骤：

![](img/aebb3b9197c83cc1d6131b8268608cd3_9.png)

```python
# 假设data[‘Close’]是股价数据
data[‘returns’] = np.log(data[‘Close’] / data[‘Close’].shift(1))
```

![](img/aebb3b9197c83cc1d6131b8268608cd3_11.png)

执行这段代码后，`data[‘returns’]`列就存储了每日的对数收益率。

![](img/aebb3b9197c83cc1d6131b8268608cd3_13.png)

---

![](img/aebb3b9197c83cc1d6131b8268608cd3_15.png)

接下来，我们需要将之前生成的交易信号（`position`列）与收益率结合起来。我们的策略逻辑是：当`position`为1时，代表我们跟随市场原始走势；当`position`为-1时，代表我们进行反向操作（做空或卖出）。

![](img/aebb3b9197c83cc1d6131b8268608cd3_17.png)

因此，策略的每日收益率应为：

`strategy_returns = position * returns`

![](img/aebb3b9197c83cc1d6131b8268608cd3_19.png)

同样，在应用`position`时，我们需要进行`shift(1)`操作，以确保使用的是前一天的信号来决定今天的操作。

![](img/aebb3b9197c83cc1d6131b8268608cd3_21.png)

以下是计算策略收益的步骤：

![](img/aebb3b9197c83cc1d6131b8268608cd3_23.png)

```python
# 对position列进行移位，使用前一天的信号
data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
```

![](img/aebb3b9197c83cc1d6131b8268608cd3_25.png)

![](img/aebb3b9197c83cc1d6131b8268608cd3_27.png)

计算完成后，`data[‘strategy’]`列就代表了策略每日的收益率。

![](img/aebb3b9197c83cc1d6131b8268608cd3_29.png)

---

为了比较长期收益，我们需要将每日的对数收益率累加起来，并还原为最终的总资产倍数。这个过程分为两步：首先对收益率序列求和，然后进行指数运算。

以下是计算总收益的步骤：

![](img/aebb3b9197c83cc1d6131b8268608cd3_31.png)

![](img/aebb3b9197c83cc1d6131b8268608cd3_33.png)

```python
# 1. 计算总对数收益
total_log_return = data[[‘returns’, ‘strategy’]].sum()

![](img/aebb3b9197c83cc1d6131b8268608cd3_35.png)

# 2. 将对数收益还原为总资产倍数
total_return = np.exp(total_log_return)
```

![](img/aebb3b9197c83cc1d6131b8268608cd3_37.png)

执行上述计算后，我们就可以得到两种策略下，初始1元资金最终变成的金额。

---

![](img/aebb3b9197c83cc1d6131b8268608cd3_39.png)

在本节课中，我们一起学习了量化策略收益分析的核心流程。我们从计算对数收益率开始，然后将交易信号与收益率结合，得到策略的每日收益，最后通过累加和指数还原，计算出策略的最终总收益。通过对比“买入并持有”的收益和我们构建的双均线策略收益，可以直观地评估策略的有效性。